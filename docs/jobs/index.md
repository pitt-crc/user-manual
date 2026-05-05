# Running Jobs

All computational work on CRCD clusters is managed through the [Slurm Workload Manager](https://slurm.schedmd.com/). Slurm handles job scheduling, resource allocation, and queue management across all clusters.

## Key Concepts

- **Job**: A unit of work submitted to the cluster for execution
- **Partition**: A logical grouping of compute nodes (e.g., `smp`, `mpi`, `gpu`)
- **QOS (Quality of Service)**: Defines job limits and priority levels
- **Service Units (SUs)**: The currency used to track compute resource usage

## Quick Reference

| Task | Command |
|------|---------|
| Submit a batch job | `sbatch job_script.slurm` |
| Start an interactive session | `srun --pty bash` |
| Check job status | `squeue -u $USER` |
| Cancel a job | `scancel <job_id>` |
| View cluster info | `sinfo` |
| View job details | `scontrol show job <job_id>` |

## Job Types

- **[Batch Jobs](../slurm/batch-jobs.md)** - Submit a script to run without interaction
- **[Interactive Jobs](../slurm/interactive-jobs.md)** - Get a shell on a compute node for real-time work
- **[Job Arrays](../slurm/job-arrays.md)** - Submit many similar jobs efficiently
- **[Scavenger Jobs](../slurm/scavenger.md)** - Use idle resources at lower priority

## Additional Topics

- [Service Units and Charges](../slurm/service-units.md) - How compute time is tracked and charged
- [PBS to Slurm Migration](../slurm/pbs-slurm.md) - Transitioning from PBS/Torque scripts
- [Scratch Storage](../slurm/scratch-storage.md) - Temporary high-performance storage for active jobs

!!! note
    All jobs must be submitted through Slurm. Running computationally intensive work directly on login nodes is prohibited and may result in process termination.
