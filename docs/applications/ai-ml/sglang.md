# Using the sglang module

This module starts an SGLang inference server of your own for the length of your job and gives you
a URL to send requests to. Like vLLM, SGLang serves regular Hugging Face (safetensors) models behind
an OpenAI-compatible API.

For choosing a GPU, reaching your server from elsewhere on the cluster, and connecting clients, see
[Inference Servers on the GPU Cluster](inference-server.md).

!!! warning "The server has no API key by default"

    Ports are open within the CRCD environment, so anyone who finds your server's host and port can
    send requests that bill against your allocation. Keep these jobs short and stop the server when
    you finish. SGLang accepts `--api-key` through `SGLANG_EXTRA_ARGS`, but a key passed that way is
    visible in the process table to anyone who can list processes on the node — see
    [Keeping your endpoint private](inference-server.md).

## Before you start

- You must be inside an active SLURM allocation — an interactive session or a batch job. Loading
  this module on a login node fails with a clear error telling you so.
- Your allocation needs a GPU (`--gres=gpu:1`). If none is visible, loading the module fails
  immediately instead of starting anything.
- SGLang needs a Turing-generation or newer GPU. Every CRCD GPU partition — `l40s`, `a100`,
  `a100_nvlink`, `rtx6k` and `h200` — qualifies.
- Ask for CPUs with **`-n1 -c16`**, not `-n16`. `-n16` gives an interactive shell, and the server
  started from it, a single core while the job is billed for sixteen.
- For a gated or private Hugging Face model (most Llama models), you'll need a Hugging Face token —
  see `SGLANG_HF_TOKEN` below.

## Quick start

```bash
srun -M gpu -p rtx6k -n1 -c16 --gres=gpu:1 -t 2:00:00 --pty bash

module load sglang/0.5.21
```

You'll see something like:

```
SGLang (container) starting - run 'sglang-status' to check readiness.
Base URL: http://gpu-n80:35238
```

With nothing else set, a small default model loads — `Qwen/Qwen2.5-0.5B-Instruct`, enough to
confirm everything works. To pick your own, set `SGLANG_MODEL` **before** loading the module, using
the repo id from the model's Hugging Face page:

```bash
export SGLANG_MODEL="Qwen/Qwen2.5-7B-Instruct"
module load sglang/0.5.21
```

SGLang serves safetensors models, not GGUF files. For GGUF, use [llama.cpp](llamacpp-module.md).

## Checking readiness

The first start of a model can take a while: SGLang downloads the weights, loads them onto the GPU,
compiles kernels, and captures CUDA graphs. Poll with:

```bash
sglang-status
```

```
READY - http://gpu-n80:35910
  Model: Qwen/Qwen2.5-7B-Instruct
```

- `READY` — you're good to go, and the `Model:` line is the name to use in requests
- `STARTING` — still downloading, loading, compiling kernels or capturing CUDA graphs; the message
  shows the last lines of the log so you can see which
- `DOWN` — it crashed; the message shows the end of the log and where the full log is

A 7B model takes about two minutes from `module load` to `READY` on an RTX PRO 6000, about 25 seconds
of it capturing CUDA graphs. That capture happens on every start.

!!! note "*'has no files matching `*.safetensors`; will attempt download'*"

    This appears on any first download of a model, and is not an error: SGLang fetches the model's
    configuration files first, then looks for weights, finds none yet, and downloads them.

!!! note "SGLang takes most of the GPU, whatever the model's size"

    SGLang reserves most of the card's memory at startup for its KV cache. Serving
    `Qwen/Qwen2.5-7B-Instruct`, whose weights are about 15 GB, `nvidia-smi` reports
    **86,915 MiB** in use on a 96 GB RTX PRO 6000. That is expected, not a leak. To leave room for
    another process on the same GPU, lower `--mem-fraction-static` — see "Common tweaks" below.

    While the server is starting, `sglang-status` may show a line ending
    `errno: 97 - Address family not supported by protocol`. It is harmless; startup continues
    normally.

## Sending requests

The module sets `$SGLANG_BASE_URL` for you:

```bash
curl "$SGLANG_BASE_URL/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -d '{"model": "Qwen/Qwen2.5-7B-Instruct",
       "messages": [{"role": "user", "content": "Hello!"}]}'
```

Most OpenAI client libraries work by pointing `base_url` at `$SGLANG_BASE_URL/v1` with any
placeholder API key. `sglang-status` prints the exact model name to use in the `"model"` field.

## Make later starts faster: keep a persistent cache

By default, downloads go to a scratch directory that disappears with your job, so every job
downloads its model again. Point the cache at your group's storage once and reuse it:

```bash
export SGLANG_DOWNLOAD_DIR="/vast/<group>/$USER/sglang-cache"
module load sglang/0.5.21
```

After the first run it holds two subdirectories, `huggingface/` for downloaded weights and
`sglang/` for SGLang's own cache. On later starts the weights load straight from it — for
`Qwen/Qwen2.5-7B-Instruct`, 14.3 GB in 1.3 seconds, with no download. CUDA graph capture still
happens on every start. Avoid your home directory, which has a 75 GB quota.

To confirm a start reused the cache, check its log:

```bash
grep -c "will attempt download" "$SGLANG_LOGFILE"      # 0 means nothing was downloaded
```

## Using a gated or private model

```bash
export SGLANG_HF_TOKEN="hf_xxx"
export SGLANG_MODEL="meta-llama/Llama-3.1-8B-Instruct"
module load sglang/0.5.21
```

Treat the token like a password, and redact it before pasting a log into a ticket.

Even for public models, the log suggests setting a token: Hugging Face applies lower rate limits to
unauthenticated downloads. `SGLANG_HF_TOKEN` is how to provide one.

## Using a model you've already downloaded

Point `SGLANG_MODEL` at the model's directory — the one containing `config.json`. A Hugging Face
cache snapshot directory works too:

```bash
export SGLANG_MODEL="/vast/<group>/$USER/models/Qwen2.5-7B-Instruct"
module load sglang/0.5.21
```

Set `SGLANG_OFFLINE=1` as well if you want to guarantee nothing reaches out to Hugging Face.

## Multiple GPUs

Request more GPUs and the module splits the model across all of them. Scale the cores with the
GPUs — sixteen per card:

```bash
srun -M gpu -p h200 -n1 -c32 --gres=gpu:2 -t 2:00:00 --pty bash
export SGLANG_MODEL="Qwen/Qwen2.5-72B-Instruct"
module load sglang/0.5.21
```

To choose the split yourself, pass `--tp-size` in `SGLANG_EXTRA_ARGS`. The number must divide evenly
into the model's attention heads — 1, 2, 4 or 8 almost always work, while 3 usually doesn't. See the
[GPU cluster](../../hardware_profiles/gpu.md) page for what each partition holds.

## Common tweaks

```bash
export SGLANG_EXTRA_ARGS="--context-length 16384 --mem-fraction-static 0.8"
module load sglang/0.5.21
```

- `--context-length N` — limit the context window, which saves memory
- `--mem-fraction-static F` — fraction of GPU memory SGLang reserves for weights plus KV cache. The
  default is chosen automatically — 0.858 on an RTX PRO 6000. Lower it if you hit out-of-memory
  errors, or if you want to run something else on the same GPU.
- `--tool-call-parser NAME` — enables OpenAI-style tool calling. It must match your model family,
  and a wrong choice gives garbled tool calls rather than an error. SGLang's parser names differ
  from vLLM's; check SGLang's documentation for your model.

Anything accepted by `python3 -m sglang.launch_server --help` can go in `SGLANG_EXTRA_ARGS`.

## Shutting down

```bash
sglang-stop          # stop the server, keep the module loaded
module unload sglang # stop it and unload the module
```

`module purge` also stops it, reporting the PID. Loading the module again stops the running server
before starting a new one, which is how to switch models. Ending your SLURM session stops the server
too.

## Troubleshooting

- **"must be loaded inside a SLURM job"** — you're on a login node. Start an interactive session or
  submit a batch job first.
- **"No GPU is visible to this job"** — add `--gres=gpu:1` to your `srun`/`sbatch` command.
- **"SGLang failed to start (not a port conflict ...)"** — usually a typo in `SGLANG_EXTRA_ARGS`.
  The error shows the end of SGLang's log with the actual complaint.
- **"NVIDIA driver is ... needs driver 580.65.06 or newer"** — the node has an older driver than
  this version needs. The `rtx6k` partition runs 595.91.07. Try a different partition, or contact
  CRCD.
- **Out of memory while loading** — request more GPUs, use a smaller model, or lower
  `--context-length` / `--mem-fraction-static`.
- **Nothing happening for a while** — check `sglang-status`. A large model's first download plus
  kernel compilation can take many minutes; a persistent `SGLANG_DOWNLOAD_DIR` makes later starts
  much faster.
- **Anything else** — the full server log is at `$SGLANG_LOGFILE`.

## Related

<div class="grid cards" markdown>

-   :material-server-network: **The serving workflow**

    ---

    Hardware sizing, finding your server, and the other ways to reach it.

    [Inference Servers](inference-server.md)

-   :material-speedometer: **The other safetensors server**

    ---

    vLLM, with a verified API-key mechanism.

    [vLLM](vllm-module.md)

-   :material-file-code: **For GGUF models**

    ---

    llama.cpp serves quantized GGUF files and a browser chat UI.

    [llama.cpp](llamacpp-module.md)

-   :material-cash: **What a job costs**

    ---

    GPU jobs are billed per card for their full walltime.

    [Service Units](../../slurm/service-units.md)

</div>
