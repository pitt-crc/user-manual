Your server lands on whichever node Slurm assigns, on a port chosen at random when the job
starts. The launch templates publish that port in the job's `Comment` field, so one `squeue`
call tells you both halves of the address:

```bash
squeue -M gpu --me --states=R -h -O JobID,Name,NodeList,Comment
```

```
1230409   vllm_server   gpu-n55   45141
```

Read that as: job `1230409` is serving on `gpu-n55:45141`. Anywhere inside the CRCD
environment, that host and port is the base address of your server.

!!! tip "Save it to a variable"

    ```bash
    read -r NODE PORT < <(squeue -M gpu --me --name=vllm_server --states=R -h -O NodeList,Comment)
    export BASE_URL="http://${NODE}:${PORT}/v1"
    ```
