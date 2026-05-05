# Batch Jobs

A brief introduction to Slurm batch jobs and a simple example can be found in the [Getting Started section](https://crc.pitt.edu/getting-started/running-jobs-slurm). This page provides detailed reference information and more complex examples.

## SBATCH Arguments

Below are the most common `sbatch` arguments. See the [full sbatch documentation](https://slurm.schedmd.com/sbatch.html) for all options.

| Argument | Description | Format/Values |
|----------|-------------|---------------|
| `--job-name` | Job name shown in `squeue` | Descriptive string. Default: JobID |
| `--nodes` | Number of nodes | Usually 1; MPI requires minimum 2 |
| `--ntasks-per-node` | Tasks to launch per node | Default: 1 |
| `--cluster` | Target cluster | `smp`, `mpi`, `gpu`, `htc` |
| `--partition` | Partition within cluster | See [hardware profiles](../systems/index.md) |
| `--time` | Maximum walltime | `days-hh:mm:ss` |
| `--qos` | Quality of Service | `short`, `normal` (default), `long` |
| `--mem` | Memory limit per node | Memory in MB (e.g., `64000`) |
| `--cpus-per-task` | CPUs per task | For multithreaded jobs |
| `--gres` | Generic resources (GPUs) | `gpu:1` for 1 GPU. Required on GPU cluster |
| `--output` | File for stdout | Path or filename |
| `--error` | File for stderr | Path or filename |
| `--mail-user` | Email for notifications | `PittID@pitt.edu` |
| `--mail-type` | Notification conditions | `END`, `FAIL`, or `END,FAIL` |

## Example Batch Script

```bash
#!/bin/bash
#SBATCH --job-name=<job_name>
#SBATCH --nodes=<number of nodes>
#SBATCH --ntasks-per-node=<tasks per node>
#SBATCH --cluster=<cluster name>
#SBATCH --partition=<partition>
#SBATCH --mail-user=<user_ID>@pitt.edu
#SBATCH --mail-type=END,FAIL
#SBATCH --time=<days-HH:MM:SS>
#SBATCH --qos=<qos>

module purge
module load module1 module2

cp <inputs> $SLURM_SCRATCH
cd $SLURM_SCRATCH
run_on_exit(){ cp -r $SLURM_SCRATCH/* $SLURM_SUBMIT_DIR; }
trap run_on_exit EXIT

srun <job executable with parameters>

crc-job-stats

cp <outputs> $SLURM_SUBMIT_DIR
```

## GPU Jobs

For GPU jobs, specify the cluster, partition, and number of GPUs:

```bash
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=<NUMBER OF TASKS>
#SBATCH --gres=gpu:<NUMBER OF GPUs>
#SBATCH --cluster=gpu
#SBATCH --partition=a100
#SBATCH --time=0-24:00:00

module purge
module load <gpu-enabled software>

<GPU command>
```

## Script Structure

### 1. Shebang line

A shebang (`#!`) line must be present: `#!/bin/bash`, `#!/bin/tcsh`, `#!/bin/env python`, etc.

### 2. SBATCH directives

Lines starting with `#SBATCH` are parsed by Slurm as directives. Other `#` lines are treated as comments by both the shell and Slurm.

### 3. Module loading

Load required software modules. Always start with `module purge` for a clean environment. See the [module system page](../applications/application-environment.md) for details.

### 4. Input handling

Set up your working environment, copy inputs to scratch, and configure traps for output preservation. The default working directory is where `sbatch` was called. Use `--chdir` to change this.

### 5. Job execution

Use `srun` to launch your application. It accepts `--nodes`, `--tasks-per-node`, and `--cpus-per-task` to adjust resources per step (cannot exceed what `sbatch` allocated).

### 6. Job statistics

Add `crc-job-stats` to report resource utilization for your job.

### 7. Output handling

Copy results from scratch back to your project directory.

## After Submission

| Command | Purpose |
|---------|---------|
| [`scancel <jobid>`](https://slurm.schedmd.com/scancel.html) | Cancel a job |
| [`squeue -M <cluster> -u $USER`](https://slurm.schedmd.com/squeue.html) | View your jobs |
| `scontrol -M <cluster> show job <jobid>` | View detailed job info |

## FAQ

### I don't receive email notifications despite setting `--mail-type` and `--mail-user`

Provide your full email as `PITTID@pitt.edu` (not just the username). Also, submitting very large numbers of jobs can fill the email queue - consider removing the mail directive for bulk submissions and use `squeue` instead.

### Where can I find example batch scripts?

Example scripts for commonly used software are in `/ihome/crc/how_to_run`. For NGS analysis examples on HTC, see [RNA-Seq data analysis](../advanced-genomics-support/RNASeq-data-analysis.md).

### How do `--nodes`, `--ntasks`, and `--cpus-per-task` interact?

- **Single-node, multi-process**: `--nodes=1 --ntasks=16` gives 16 independent processes on one node
- **Multi-node MPI**: `--nodes=4 --ntasks=64` distributes 64 MPI processes across 4 nodes
- **Multi-node with control**: `--nodes=4 --ntasks-per-node=16` explicitly places 16 tasks per node
- **Multi-threaded**: `--ntasks=1 --cpus-per-task=16` gives 1 process with 16 threads on one node

### Slurm ignores my `.bashrc` settings

Slurm does not source `~/.bashrc` or `~/.profile` by default. Add `source ~/.bashrc` in your script after `module load` commands if needed.

### How do I check which allocation my jobs charge?

```bash
sacctmgr show associations onlydefaults | grep USERNAME
```

To charge a different allocation:

```bash
#SBATCH --account=GROUPNAME
```

### How do I submit many similar jobs efficiently?

See [Job Arrays](./job-arrays.md) for submitting collections of parameterized jobs.
