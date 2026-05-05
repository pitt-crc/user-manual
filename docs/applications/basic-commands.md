# Basic Slurm Commands

Quick reference for the most common [Slurm](../slurm/slurm-overview.md) commands:

## Essential Commands

| Command | Description |
|---------|-------------|
| `sinfo` | View hardware allocation and availability |
| `sbatch <script>` | Submit a batch job |
| `squeue -u $USER` | View your running and queued jobs |
| `scancel <jobid>` | Cancel a job |
| `sshare` | View fairshare information |
| `sprio` | View queued job priorities |

## PBS to Slurm Equivalents

| Action | PBS/Torque | Slurm |
|--------|-----------|-------|
| Submit a job | `qsub job_script` | `sbatch job_script` |
| Submit with options | `qsub -q queue -l nodes=1:ppn=16 -l mem=64g script` | `sbatch --partition=queue --nodes=1 --cpus-per-node=16 --mem=64g script` |
| Node count | `-l nodes=count` | `--nodes=1` |
| Cores per node | `-l ppn=count` | `--cpus-per-node=count` |
| Memory size | `-l mem=16384` | `--mem=16g` |
| Job name | `-N name` | `--job-name=name` |

## Key SBATCH Arguments

| Argument | Description |
|----------|-------------|
| `--nodes` | Maximum number of nodes for the job |
| `--tasks-per-node` | Number of tasks to launch per node |
| `--cpus-per-task` | Number of CPUs per task (for multithreaded programs) |
| `--error` | File to redirect standard error |
| `--job-name` | Job name (shown in `squeue` output) |
| `--time` | Maximum walltime (format: `days-hh:mm:ss`) |
| `--cluster` | Target cluster: `smp`, `mpi`, `gpu`, or `htc` |
| `--partition` | Target partition within the cluster |
| `--account` | Allocation to charge (only needed for multi-allocation users) |

See the [sbatch documentation](https://slurm.schedmd.com/sbatch.html) for the full list of options.

## Understanding Tasks vs CPUs

- **`--ntasks`**: Number of processes (use for MPI programs)
- **`--cpus-per-task`**: CPUs per process (use for multithreaded programs)

A multi-process MPI program uses `--ntasks`. A single-process multithreaded program uses `--ntasks=1 --cpus-per-task=N`. Individual tasks cannot span multiple nodes, so `--cpus-per-task` always allocates cores on a single node.

!!! note
    The shebang line (`#!/bin/bash`) must be present at the top of batch scripts. SBATCH directives are placed after it using the `#SBATCH` prefix.
