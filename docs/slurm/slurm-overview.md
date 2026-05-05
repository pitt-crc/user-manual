# Slurm Overview

The CRCD clusters use [Slurm](http://slurm.schedmd.com/) for job scheduling and resource management.

## Viewing Cluster Status

Use `sinfo` to see the state of nodes across clusters:

```commandline
sinfo -M smp,gpu,mpi,htc
```

!!! note
    The `-M` flag for `sinfo`, `scontrol`, `sbatch`, and `scancel` specifies which cluster(s) to query. Without this flag, commands default to the primary cluster of the login node you are on (SMP on h2p.crc.pitt.edu, HTC on htc.crc.pitt.edu).

## Node States

- **alloc** - A job is running on the node
- **idle** - The node is available
- **mix** - Some cores are allocated, some are free

The asterisk (`*`) next to a partition name indicates the default partition.

## Viewing the Job Queue

```commandline
squeue -M <cluster>
```

### Common Job States

| Code | State | Description |
|------|-------|-------------|
| CA | CANCELLED | Job was explicitly cancelled by the user or administrator |
| CD | COMPLETED | Job has terminated all processes on all nodes |
| CG | COMPLETING | Job is finishing; some processes may still be active |
| F | FAILED | Job terminated with a non-zero exit code |
| PD | PENDING | Job is awaiting resource allocation |
| R | RUNNING | Job currently has an allocation |
| TO | TIMEOUT | Job terminated upon reaching its time limit |

See `man squeue` or the [Slurm documentation](https://slurm.schedmd.com/squeue.html#SECTION_JOB-STATE-CODES) for the complete list.

## Checking Expected Start Time

```commandline
squeue --start
```

!!! note
    Not all pending jobs have a definite start time.

## Viewing Job Details

```commandline
scontrol -M <cluster> show job <jobid>
```

This shows detailed information for pending or running jobs, including resource allocation, time limits, and node assignments.
