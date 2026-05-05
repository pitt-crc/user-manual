# Service Units

One service unit (SU) is approximately equal to 1 core-hour of computing. The charge is calculated based on Slurm's Trackable Resources (TRES):

- Number of cores requested
- RAM requested
- Number of GPUs requested (GPU cluster only)

## TRES Billing Weights

| Cluster | Partition | Compute Weight (Per CPU/GPU) | Memory Weight (Per GB) |
|---------|-----------|------------------------------|------------------------|
| **SMP** | smp | 0.8 | 0.102 |
| | high-mem | 1.0 | 0.0477 |
| | preempt | 0 | 0 |
| **MPI** | mpi | 1 | 0.93 |
| | ndr | 1 | 0.93 |
| | preempt | 0 | 0 |
| **GPU** | a100 | 8 | 0 |
| | a100_multi | 8 | 0 |
| | a100_nvlink | 8 | 0 |
| | l40s | 8 | 0 |
| | preempt | 0 | 0 |
| **HTC** | htc | 1 | 0.128 |
| | preempt | 0 | 0 |

## Viewing Partition Billing Configuration

Use `scontrol` to see the full TRES billing weights for a cluster:

```commandline
scontrol -M htc show partition
```

## Checking Job Resource Usage

Use `sacct` to see the TRES consumed by a completed job:

```commandline
sacct -X -M smp -j <jobid> --format=User,JobID,Jobname,AllocTRES%30,Elapsed
```

Example output:

```
     User        JobID    JobName                      AllocTRES    Elapsed
--------- ------------ ---------- ------------------------------ ----------
    nlc60 6169876      hello_wor+         cpu=1,mem=4018M,node=1   00:00:01
```
