# Using the vllm module

Loading this module starts a private vLLM inference server for you and
gives you a URL to talk to it. Unloading it (or ending your job) shuts
the server down again.

## Before you start

- You must be inside an active SLURM allocation — an interactive
  session (`salloc`, `srun --pty bash`) or a batch job. Loading this
  module on a login node will fail with a clear error telling you so.
- Your allocation needs an actual GPU (e.g. `srun --gres=gpu:1 ...`).
  If none is visible, loading the module fails immediately with a
  clear error rather than starting anything.
- If you're serving a gated or private HuggingFace model (most Llama
  models, some others), you'll need a HuggingFace token — see
  `VLLM_HF_TOKEN` below.

## Quick start

```bash
srun -M gpu -p rtx6k -n16 --gres=gpu:1 -t21:00:00 --pty bash
module load vllm/0.29.0
```

With nothing else set, this loads a small default model just to
confirm everything works, and prints something like:

```
vLLM (container) starting on gpu042:41317 (model: facebook/opt-125m)
Base URL: http://gpu042:41317  |  run 'vllm-status' to check readiness, 'vllm-stop' to end it early.
```

!!! failure "Did not work"
    If this is your first time using vLLM, you may encounter the error below:

    ```bash
    [kimwong@gpu-n78.crc.pitt.edu ~]$module load vllm/0.29.0
    Lmod has detected the following error:  Failed to start vLLM:
    WARN: vLLM container exited immediately on port 52055 (attempt 1), log tail:
    WARNING: skipping mount of /xhome/crc/kimwong/.cache/huggingface: stat /xhome/crc/kimwong/.cache/huggingface: no such file or directory
    INFO: Terminating squashfuse_ll after timeout
    ...
    INFO: Timeouts can be caused by a running background process
    FATAL: container creation failed: mount hook function failure: mount /xhome/crc/kimwong/.cache/huggingface->/xhome/crc/kimwong/.cache/huggingface error: while
    mounting /xhome/crc/kimwong/.cache/huggingface: mount source /xhome/crc/kimwong/.cache/huggingface doesn't exist
    ERROR: vLLM failed to start after 5 attempts.
    ```

    The error can be fixed by creating the cache folder:

    ```bash
    [kimwong@gpu-n78.crc.pitt.edu ~]$mkdir -p ~/.cache/huggingface
    [kimwong@gpu-n78.crc.pitt.edu ~]$module load vllm/0.29.0
    vLLM (container) starting on gpu-n78:52071 (model: facebook/opt-125m)
    Base URL: http://gpu-n78:52071  |  run 'vllm-status' to check readiness, 'vllm-stop' to end it early.
    [kimwong@gpu-n78.crc.pitt.edu ~]$
    ```    

## Loading a real model

Set `VLLM_MODEL` *before* loading the module:

```bash
export VLLM_MODEL="meta-llama/Llama-3.1-8B-Instruct"
module load vllm/0.29.0
```

All the environment variables below work the same way: set them, then
load the module. The module reads them once, at load time — changing
them afterward has no effect until you unload and reload.

## Using a model that's already on disk (no internet access)

If you're on a cluster that can't reach huggingface.co, `VLLM_MODEL`
can be a local path instead of a repo id — just point it at a
directory that already has the model's files in it:

```bash
export VLLM_MODEL="/software/rhel9/manual/models/llama-3.1-8b-instruct"
module load vllm/0.29.0
```

That's it — a path starting with `/` is detected automatically. The
module makes sure the container can see that directory and tells vLLM
not to bother trying the network, so there's no hanging or timeout
waiting on a connection that isn't there.

If the model directory doesn't exist at all, loading fails
immediately with a clear error. If it exists but looks incomplete (no
`config.json`), you'll get a warning in the log — usually that means
you pointed at the wrong folder inside a HuggingFace cache download
(the cache nests things under `models--org--name/snapshots/<hash>/` —
you want that innermost `snapshots/<hash>/` folder, or better, ask
whoever downloaded the model to use `--local-dir` instead, which skips
that nesting entirely).

Getting a model onto the cluster in the first place is a separate,
one-time step someone with network access needs to do — ask your
cluster admin if you're not sure how models get staged for offline use
at your site.

## Environment variables

### Commonly used

| Variable | What it does | Default |
|---|---|---|
| `VLLM_MODEL` | HuggingFace model id, or a local path (starting with `/`) to a pre-downloaded model — see above | `facebook/opt-125m` |
| `VLLM_HF_TOKEN` | Your HuggingFace token, for gated/private models. Not needed if you've already run `huggingface-cli login` in this shell. | none |
| `VLLM_DOWNLOAD_DIR` | Where model weights *and* vLLM's own cache (compiled kernels, autotune results, etc.) get stored, if you don't want them under `~/.cache/` — handy if your home directory has limited space | HF/vLLM's default caches under `$HOME` |
| `VLLM_EXTRA_ARGS` | Extra flags passed straight to vLLM, e.g. `"--max-model-len 8192 --gpu-memory-utilization 0.9"` | none |

### Occasionally useful

| Variable | What it does |
|---|---|
| `VLLM_EXCLUDE_PORTS` | Space-separated extra ports to avoid (8000 is always avoided automatically) |
| `VLLM_IMAGE` | Path to a different `.sif` image, if you need a build other than this module's default |
| `VLLM_OFFLINE` | Force offline mode even when `VLLM_MODEL` looks like a repo id rather than a path (a local path already forces this automatically) |

### Advanced (you likely won't need these)

`VLLM_CONTAINER_GPU_FLAG`, `VLLM_CONTAINER_BINDS`, `VLLM_CONTAINER_ARGS`,
`VLLM_CONTAINER_RUNTIME`, `VLLM_APPTAINER_MODULE` — these exist for
working around site-specific container quirks. Run `module help vllm`
for what each one does.

## What you get after loading

The module sets these in your shell:

- `$VLLM_BASE_URL` — full URL to your server, e.g. `http://gpu042:41317`
- `$VLLM_HOST`, `$VLLM_SERVER_PORT` — the same, split apart
- `$VLLM_LOGFILE` — path to the server's log
- `$VLLM_PID` — the server's process ID

## Checking readiness

Big models can take a few minutes to load. Run:

```bash
vllm-status
```

This tells you whether the server is still starting up, up and
serving, or has crashed — along with the last lines of its log either
way.

## Talking to the server

Once it's up, it's a normal OpenAI-compatible endpoint:

```bash
curl "$VLLM_BASE_URL/v1/models"

curl "$VLLM_BASE_URL/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -d '{
        "model": "meta-llama/Llama-3.1-8B-Instruct",
        "messages": [{"role": "user", "content": "Hello!"}]
      }'
```

Use the model id you actually set in `VLLM_MODEL` — vLLM registers the
model under that name.

## Shutting it down

```bash
vllm-stop            # stop the server, keep the module loaded
# or
module unload vllm   # stop it and unload the module
```

If you end your SLURM session without doing either, SLURM cleans the
server up automatically when the job exits — you won't leave anything
running behind.

## Troubleshooting

- **"must be run inside a SLURM allocation"** — you're on a login
  node. Start an interactive session or submit a batch job first.
- **"No GPU is visible to this job"** — your allocation doesn't have
  a GPU attached. Re-request your session with a GPU, e.g. add
  `--gres=gpu:1` to your `salloc`/`srun`/`sbatch` command, then load
  the module again.
- **Nothing happening for a while** — check `vllm-status`; large
  models can genuinely take several minutes to load, this is normal.
- **"VLLM_MODEL looks like a local path but no such directory exists"**
  — either the path is wrong, or it exists but isn't visible from the
  compute node you landed on (e.g. it's on storage only mounted on
  some nodes). Double check the exact path and that it's reachable
  from inside a job, not just from the login node.
- **A warning about a missing `config.json`** — you likely pointed at
  the wrong folder inside a HuggingFace-style cache directory; see
  "Using a model that's already on disk" above for which folder you
  actually want.
- **401/403 errors in the log while downloading the model** (only
  relevant if you're using an HF repo id with network access, not a
  local path) — the model is gated. Set `VLLM_HF_TOKEN`, or request
  access to the model on HuggingFace first.
- **Out of memory** — try a smaller model, or lower memory usage with
  `export VLLM_EXTRA_ARGS="--gpu-memory-utilization 0.85"` (or lower)
  before loading.
- **Something seems stuck or wrong after a failed load** — `module
  unload vllm` then `module load vllm/0.29.0` again; the module always
  looks for a fresh free port, so this is safe to retry.
