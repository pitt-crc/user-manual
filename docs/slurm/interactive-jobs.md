# Interactive Jobs

Interactive jobs give you a shell on a compute node for real-time work such as testing, debugging, benchmarking, or running tutorials without impacting other users on the login nodes.

## Submitting an Interactive Job

Use `srun` to request an interactive session:

```commandline
srun -M smp -n1 -t02:00:00 --pty bash
```

This requests 1 core on the SMP cluster for 2 hours. When the job starts, your prompt changes to reflect the compute node:

```commandline
[fangping@login0a ~]$ srun -M smp -n1 -t02:00:00 --pty bash
[fangping@smp-n45 ~]$
```

Other clusters (HTC, MPI, GPU) are also available via `-M <cluster>`.

!!! note
    Interactive jobs consume service units from your allocation. If your account is associated with multiple allocations, specify which to charge with `-A <account_name>`. Check your default with:
    ```
    sacctmgr show assoc onlydefaults | grep USERNAME
    ```

## Interactive Jobs with X11 Forwarding

For GUI applications, allocate a node first with `salloc`, then SSH with X11:

```commandline
salloc -M htc -n1 -t02:00:00
```

Once resources are granted:

```commandline
ssh -X htc-n12
```

You can then launch GUI applications from the command line on that node.

## Open OnDemand Alternative

[Open OnDemand](../web-portals/open-ondemand.md) provides a web-based interface for launching interactive sessions with GUI tools (RStudio, Jupyter, MATLAB) without needing to use Slurm commands directly.

## Quality of Service (QoS)

All jobs must be assigned a QoS, which defines walltime limits and priority. The default is `normal`.

| QoS | Max Walltime | Priority Factor |
|-----|-------------|-----------------|
| short | 1-00:00:00 | 1.0 |
| normal | 3-00:00:00 | 0.75 |
| long | 6-00:00:00 | 0.5 |

Jobs that exceed the walltime limit for their QoS will not be accepted by the scheduler.
