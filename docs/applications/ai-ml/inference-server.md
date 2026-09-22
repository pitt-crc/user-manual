# Inference Servers on the GPU Cluster

An inference server loads a model onto a GPU once, then answers requests over HTTP for as long
as the job runs. Instead of paying the model-loading cost on every script invocation, you submit
one Slurm job that holds the GPU and serves an HTTP endpoint, then point as many clients at it as
you like — a notebook, an R session, a batch of analysis scripts, or your laptop.

Most of the servers documented here expose an **OpenAI-compatible API**, so any client library
that talks to OpenAI can talk to your server by changing the base URL.

!!! danger "Never run a server on a login node"

    Login nodes are shared by everyone and have no GPUs. A server process started there will be
    killed. Always request a GPU through Slurm first, as shown below.

The workflow is the same regardless of which framework or access method you choose:

1. Pick hardware that fits your model.
2. Submit a job that starts the server.
3. Find the node and port it landed on.
4. Connect a client.
5. Cancel the job when you're finished.

## Before you start

You need an allocation on the `gpu` cluster. If jobs sit in `PD` state with reason
`AssocGrpBillingMinutes`, you have no service units to draw from — request an allocation with a
[Service Request Form](https://crc.pitt.edu/service-request-forms).

Model weights are large and your home directory has a 75 GB quota, so send downloads elsewhere
before you start. The variable to set depends on the framework:

| Framework | Cache variable | Covers |
| --------- | -------------- | ------ |
| vLLM | `VLLM_DOWNLOAD_DIR` | Weights and vLLM's compiled-kernel cache |
| Ollama | `OLLAMA_MODELS` | Pulled models |
| llama.cpp | n/a | You supply a `.gguf` path directly |
| TEI | `HF_HOME` | Weights |

```bash
export VLLM_DOWNLOAD_DIR=/vast/<group>/$USER/vllm
```

Use your group's `/vast` (our recommendation) or `/ix1` space for weights you'll reuse, or `$SLURM_SCRATCH` for
one-off experiments. See [Storage Tiers](../../hardware_profiles/storage.md) and
[Scratch Space](../../slurm/scratch-storage.md).

Gated models on Hugging Face (the Llama family, for example) require you to accept the license on
the model's page and supply a token (keep this info private and secure; treat it like a password). For vLLM, pass it as `VLLM_HF_TOKEN`:

```bash
export VLLM_HF_TOKEN=hf_xxxxxxxxxxxxxxxxxxxx
```

Alternatively, run `huggingface-cli login` once in the shell before loading the module. Keep the
token out of scripts you commit or share.

## Step 1. Choose hardware for your model

Weights in 16-bit precision occupy roughly 2 GB per billion parameters, and the KV cache for
in-flight requests needs headroom on top of that. Plan on filling no more than about 80% of a
GPU card's VRAM with weights.

| Model size (16-bit) | Approx. weights | Fits on | [Slurm directives](../../slurm/batch-jobs.md)|
| ------------------- | --------------- | ------- | ------- |
| 7–8 B | ~16 GB | 1× L40S | `-p l40s --gres=gpu:1` |
| 13–14 B | ~28 GB | 1× L40S | `-p l40s --gres=gpu:1` |
| 30–34 B | ~68 GB | 1× RTX PRO 6000, or 2× L40S | `-p rtx6k --gres=gpu:1` |
| 70 B | ~140 GB | 2× RTX PRO 6000, 4× L40S, or 1× H200 | `-p rtx6k --gres=gpu:2` |
| 100 B+ | 200 GB+ | 8× H200 or 8× A100 80 GB NVLink | `-p h200 --gres=gpu:8` |

Quantized weights (AWQ, GPTQ, 8-bit, or GGUF) cut these figures by half or more, which often moves
a model down a tier. Full per-partition specifications, including the `--constraint` values for
pinning a specific card or host CPU, are on the [GPU cluster](../../hardware_profiles/gpu.md) page.

!!! note "Multiple cards need to be told to cooperate"

    Requesting `--gres=gpu:4` does not by itself spread a model across four cards. Each framework
    has its own flag — `--tensor-parallel-size` for vLLM, `--split-mode` for llama.cpp. Set it to
    match the number of GPUs you requested. For models spanning many cards, the `h200` and
    `a100_nvlink` partitions connect their GPUs with NVLink and will outperform PCIe partitions.

## Step 2. Launch the server

Ready-to-run templates are installed on the cluster. Copy a template to your own space and edit it:

```bash
cp /software/rhel9/manual/install/inference-templates/vllm/vllm_l40s.slurm ~/my_server.slurm
```

The annotated versions below show what each template does, so you can adapt them.

=== "vLLM"

    High-throughput serving with continuous batching and an OpenAI-compatible API. This is the
    default choice for serving a Hugging Face model to more than one client.

    **Loading the `vllm` module starts a server.** There is no separate launch command. The module
    reads its `VLLM_*` settings once, at load time, chooses a free port itself, and exports the
    address it picked. It also refuses to load outside a Slurm allocation, so you cannot start one
    on a login node by accident.

    For interactive work, the commands below can be executed on the terminal commandline:

    ```bash
    srun -M gpu -p l40s -n16 --gres=gpu:1 -t 2:00:00 --pty bash
    export VLLM_MODEL="meta-llama/Llama-3.1-8B-Instruct"
    export VLLM_DOWNLOAD_DIR=/vast/<group>/$USER/vllm
    module load vllm/0.29.0
    vllm-status                      # readiness; large models take a few minutes
    curl "$VLLM_BASE_URL/v1/models"
    ```

    The module leaves `$VLLM_BASE_URL`, `$VLLM_HOST`, `$VLLM_SERVER_PORT`, `$VLLM_LOGFILE`, and
    `$VLLM_PID` in your shell, and gives you `vllm-status` and `vllm-stop`. Full reference:
    [Using the vllm module](vllm-module.md).

    For a long-lived server you want a batch job instead, which needs two things the interactive
    path gets for free — a way for clients to discover the port, and something to stop the script
    from exiting:

    ```bash
    #!/bin/bash
    #SBATCH --job-name=vllm_server
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --nodes=1
    #SBATCH --cpus-per-task=16
    #SBATCH --gres=gpu:1
    #SBATCH --mem=120G
    #SBATCH --time=8:00:00
    #SBATCH --output=%x_%j.out

    # Every VLLM_* export must come before the module load
    export VLLM_MODEL="meta-llama/Llama-3.1-8B-Instruct"
    export VLLM_DOWNLOAD_DIR=/vast/<group>/$USER/vllm
    export VLLM_EXTRA_ARGS="--tensor-parallel-size 1 --api-key $INFERENCE_TOKEN"

    module purge
    module load vllm/0.29.0

    # Publish the port the module chose, so clients elsewhere can find it
    scontrol update JobId="$SLURM_JOB_ID" Comment="$VLLM_SERVER_PORT"

    # Hold the allocation open: the server runs in the background, so if this script
    # reaches the end, SLURM ends the job and the server goes with it
    while kill -0 "$VLLM_PID" 2>/dev/null; do sleep 60; done
    ```

    The installed templates add readiness polling and per-job API key generation around this
    skeleton.

    !!! warning "Exports have to precede the module load"

        `VLLM_MODEL`, `VLLM_DOWNLOAD_DIR`, `VLLM_EXTRA_ARGS`, and `VLLM_HF_TOKEN` are read at load
        time only. Setting one afterward changes nothing until you `module unload vllm` and load it
        again. With no `VLLM_MODEL` set you get `facebook/opt-125m`, a tiny model whose only
        purpose is to prove the plumbing works.

    Anything vLLM itself accepts can go in `VLLM_EXTRA_ARGS`, including
    `--tensor-parallel-size` for multiple GPUs, `--max-model-len`, and
    `--gpu-memory-utilization`.

    **Serving a model already staged on disk.** `VLLM_MODEL` also accepts an absolute path.
    A value starting with `/` is detected automatically: the module binds that directory into
    the container and forces offline mode, so nothing is downloaded and no HuggingFace token is
    needed even for a gated model.

    ```bash
    export VLLM_MODEL="/software/rhel9/manual/models/llama-3.1-8b-instruct"
    module load vllm/0.29.0
    ```

    Prefer a staged model when one exists. It starts faster, needs no network, doesn't consume
    your storage quota, and sidesteps license gating. Set `VLLM_OFFLINE=1` to force offline mode
    for a repo id as well.

    !!! tip "Pointing at the right folder"

        If you stage a model yourself with `huggingface-cli`, use `--local-dir`. A plain
        `download` leaves the files nested under `models--org--name/snapshots/<hash>/`, and
        `VLLM_MODEL` needs that innermost directory, not the top of the cache. A missing
        `config.json` warning in the log is the symptom of pointing one level too high.

=== "Ollama"

    Simplest option for interactive experimentation and for pulling community models by name.
    Lower throughput than vLLM under concurrent load. Ollama is installed as a Singularity image;
    see the [Ollama](ollama.md) page for details on pulling models.

    ```bash
    #!/bin/bash
    #SBATCH --job-name=ollama_server
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --nodes=1
    #SBATCH --cpus-per-task=16
    #SBATCH --gres=gpu:1
    #SBATCH --mem=125G
    #SBATCH --time=8:00:00
    #SBATCH --output=%x_%j.out

    module purge
    module load singularity/4.3.2

    export OLLAMA_MODELS=/vast/<group>/$USER/ollama_models

    PORT=$(python3 -c 'import socket;s=socket.socket();s.bind(("",0));print(s.getsockname()[1]);s.close()')
    scontrol update JobId=$SLURM_JOB_ID Comment=$PORT
    export OLLAMA_HOST=0.0.0.0:$PORT

    echo "Serving on $(hostname):$PORT"

    singularity exec --nv -B /ix1 \
        /software/rhel9/manual/install/ollama/ollama-0.11.10.sif \
        ollama serve
    ```

    Ollama also answers on an OpenAI-compatible path at `/v1`, so the client examples below work
    against it with `api_key="ollama"`.

=== "llama.cpp"

    Best fit for quantized GGUF models, including running a large model on a single smaller card.
    Very low startup latency.

    ```bash
    #!/bin/bash
    #SBATCH --job-name=llamacpp_server
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --nodes=1
    #SBATCH --cpus-per-task=16
    #SBATCH --gres=gpu:1
    #SBATCH --mem=125G
    #SBATCH --time=8:00:00
    #SBATCH --output=%x_%j.out

    module purge
    module load llama.cpp/<VERSION>

    MODEL=/vast/<group>/$USER/models/model-Q4_K_M.gguf

    PORT=$(python3 -c 'import socket;s=socket.socket();s.bind(("",0));print(s.getsockname()[1]);s.close()')
    scontrol update JobId=$SLURM_JOB_ID Comment=$PORT

    echo "Serving on $(hostname):$PORT"

    llama-server \
        --model "$MODEL" \
        --host 0.0.0.0 \
        --port "$PORT" \
        --n-gpu-layers 999 \
        --ctx-size 8192
    ```

    `--n-gpu-layers 999` offloads every layer it can to the GPU. Lower it if the model doesn't fit
    and you're willing to run part of it on the CPU.

=== "Text Embeddings Inference"

    For embedding and reranking models rather than text generation — useful for retrieval
    pipelines over a large document corpus.

    ```bash
    #!/bin/bash
    #SBATCH --job-name=tei_server
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --nodes=1
    #SBATCH --cpus-per-task=8
    #SBATCH --gres=gpu:1
    #SBATCH --mem=64G
    #SBATCH --time=12:00:00
    #SBATCH --output=%x_%j.out

    module purge
    module load singularity/4.3.2

    export HF_HOME=/vast/<group>/$USER/hf_cache

    PORT=$(python3 -c 'import socket;s=socket.socket();s.bind(("",0));print(s.getsockname()[1]);s.close()')
    scontrol update JobId=$SLURM_JOB_ID Comment=$PORT

    echo "Serving on $(hostname):$PORT"

    singularity exec --nv -B /ix1 \
        /software/rhel9/manual/install/tei/<IMAGE>.sif \
        text-embeddings-router \
        --model-id BAAI/bge-large-en-v1.5 \
        --hostname 0.0.0.0 \
        --port "$PORT"
    ```

    Embedding models are small, so one card is almost always enough. A single L40S will serve
    thousands of documents per minute.

!!! warning "Avoid preemptible partitions for servers"

    A preempted job takes your endpoint down mid-request. Run servers on regular partitions. See
    [Preemptible Partitions](../../slurm/preempt.md).

After you have adapted the template to your liking, submit the job to Slurm:

```bash
sbatch ~/my_server.slurm
```

## Step 3. Find the host and port

--8<-- "inference-discovery.md"

## Step 4. Connect a client

Cross-cluster access is intentional and ports are open within the CRCD environment, so a server on
the `gpu` cluster is reachable from HTC, SMP, OnDemand sessions, and login nodes without any
tunneling. Choose the tab matching where your client runs.

=== "Another cluster node"

    From a login node or any interactive job, check that the server is up:

    ```bash
    curl -s -H "Authorization: Bearer $(cat ~/.crcd_inference/token_1230409)" \
         http://gpu-n55:45141/v1/models
    ```

    Then use it from Python:

    ```python
    from pathlib import Path
    from openai import OpenAI

    client = OpenAI(
        base_url="http://gpu-n55:45141/v1",
        api_key=open(f"{Path.home()}/.crcd_inference/token_1230409").read().strip(),
    )

    resp = client.chat.completions.create(
        model="meta-llama/Llama-3.1-8B-Instruct",
        messages=[{"role": "user", "content": "Summarize this abstract: ..."}],
    )
    print(resp.choices[0].message.content)
    ```

    This is the pattern to use for batch analysis: submit your server once, then submit many
    lightweight CPU-only jobs on HTC that all query the same endpoint. Those client jobs need no
    GPU, so they queue quickly and cost far less.

=== "Jupyter or RStudio on OnDemand"

    Launch a session from **Interactive Apps** at
    [ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu) — an HTC session is fine and
    cheaper, since the GPU work happens in your server job. In a notebook cell, point a client at
    the address from Step 3, exactly as in the previous tab.

    From R, the `ellmer` or `rollama` packages take a base URL the same way:

    ```r
    library(ellmer)
    chat <- chat_openai(
      base_url = "http://gpu-n55:45141/v1",
      api_key  = readLines("~/.crcd_inference/token_1230409"),
      model    = "meta-llama/Llama-3.1-8B-Instruct"
    )
    chat$chat("Summarize this abstract: ...")
    ```

    See [Jupyter on OnDemand](../../web-portals/jupyter-ondemand.md) and
    [RStudio Server on the GPU Cluster](../ondemand-rstudio-gpu.md).

=== "A browser (OnDemand node proxy)"

    Servers that ship a web interface can be opened directly in your browser through OnDemand's
    node proxy. Build the URL from the node and port in Step 3:

    ```
    https://ondemand.htc.crc.pitt.edu/node/gpu-n55.crc.pitt.edu/45141/
    ```

    For vLLM, appending `docs` to that URL gives you interactive API documentation where you can
    fire test requests without writing any code. llama.cpp serves a built-in chat interface at the
    root path.

    You must be logged in to OnDemand in the same browser for the proxy to authorize you.

=== "Your laptop (SSH tunnel)"

    Compute nodes aren't reachable from off-cluster, so forward the port through a login node:

    ```bash
    ssh -N -L 8000:gpu-n55:45141 <pittID>@h2p.crc.pitt.edu
    ```

    Leave that running. The server is now at `http://localhost:8000/v1` on your laptop, usable
    from a local script, a desktop chat client, or your browser. You'll need to be on
    Wireless-PittNet or the University VPN. See
    [SSH Connection Using a Terminal](../../web-portals/terminal.md).

=== "VS Code"

    With a Remote-SSH session on the cluster, VS Code forwards ports from the remote host
    automatically. Open the **Ports** panel, add the server's port, and reach it at
    `localhost:<port>` on your machine. See [VS Code](../../applications/coding/vscode.md).

## Step 5. Stop the server

A server job holds its GPU and bills service units for the full walltime whether it's answering
requests or sitting idle. GPU jobs are billed per card, so an idle 8-card server is expensive.
Cancel it as soon as you're done:

```bash
scancel -M gpu 1230409
```

Set `--time` to what you actually need rather than the maximum. For a long-running service, it's
usually cheaper to relaunch on demand than to hold a card overnight.

## Keeping your endpoint private

All ports are open within the CRCD environment, which is what makes cross-cluster clients easy —
and it also means any user on any cluster could send requests to an unauthenticated server. Your
allocation pays for their tokens, and your prompts and completions pass through a process they
could query.

- Always set an API key. vLLM and TEI accept one directly; put llama.cpp and Ollama behind a
  key-checking proxy if the endpoint matters.
- Generate the key per job rather than reusing a fixed string, as the templates above do.
- Store it in a file with restrictive permissions, not in a script or a notebook cell. The
  templates write it to `~/.crcd_inference/token_<jobid>` and delete it when the job ends.
- Don't serve models or data with licensing or privacy restrictions without confirming the terms.
  For restricted data, contact CRCD before you begin.

## Troubleshooting

| Symptom | Likely cause | What to do |
| ------- | ------------ | ---------- |
| Job stays `PD` with `(Resources)` | Requested partition is busy | Target a partition with more free cards, or fewer GPUs |
| Job stays `PD` with `AssocGrpBillingMinutes` | No allocation or expired | Submit a [Service Request Form](https://crc.pitt.edu/service-request-forms) |
| `Connection refused` from a client | Server still loading, or bound to localhost | Check the job output; confirm `--host 0.0.0.0` |
| `CUDA out of memory` at startup | Model too large for the cards | Fewer layers, a quantized model, more GPUs, or a larger-VRAM partition |
| Empty `Comment` from `squeue` | Job hasn't reached the `scontrol update` line | Wait a moment and rerun; check the output file for errors |
| `must be run inside a SLURM allocation` | Loaded the `vllm` module on a login node | Start an interactive session or submit a batch job first |
| `No GPU is visible to this job` | Allocation has no GPU attached | Add `--gres=gpu:1`; an HTC or SMP session cannot host the server |
| `VLLM_MODEL looks like a local path but no such directory exists` | Wrong path, or storage not mounted on the node you landed on | Confirm the path is readable from inside a job, not just from a login node |
| Server serves `facebook/opt-125m` | `VLLM_MODEL` unset, or set after the module load | Export it first, then `module unload vllm && module load vllm/0.29.0` |
| Batch job ends seconds after starting | Script fell off the end; the module backgrounds the server | Block on `$VLLM_PID` as the templates do |
| `vllm-status` reports still starting | Normal for large models | Wait; check `$VLLM_LOGFILE` for progress |
| `401` or `403` from the server | Wrong or missing API key | Compare against the token file named in the job output |
| Server vanished mid-session | Walltime expired, or job was preempted | Check `sacct -M gpu -j <jobid>`; avoid preemptible partitions |

If you're stuck, submit a [help ticket](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and include the job ID and the job's output file.

## Related

<div class="grid cards" markdown>

-   :material-expansion-card-variant: **Pick a GPU**

    ---

    Per-partition cards, VRAM, cores, and `--constraint` values.

    [GPU cluster](../../hardware_profiles/gpu.md)

-   :material-book-open-variant: **vllm module reference**

    ---

    Every `VLLM_*` variable, `vllm-status`, and `vllm-stop`.

    [Using the vllm module](vllm-module.md)

-   :material-console: **Ollama specifics**

    ---

    Pulling models, and the R and Python clients CRCD has preinstalled.

    [Ollama](ollama.md)

-   :material-cash: **What a job costs**

    ---

    GPU jobs are billed per card for their full walltime.

    [Service Units](../../slurm/service-units.md)

-   :material-package-variant-closed: **Bring your own server**

    ---

    Build a container for a framework CRCD doesn't provide as a module.

    [Introduction to Singularity](../singularity.md)

</div>
