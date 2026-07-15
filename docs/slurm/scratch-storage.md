# Scratch Space for Faster I/O

## What scratch space is

**Scratch** is fast, temporary storage on high-speed local SSDs attached
directly to each compute node. Because it's local to the node, reading and
writing to it avoids the network round-trips of the shared filesystems, which
can dramatically speed up I/O-heavy jobs.

Slurm creates a private scratch directory for each job and exposes its path in
the `$SLURM_SCRATCH` environment variable (typically `/scratch/slurm-<JobID>`).

!!! warning "Scratch is deleted when your job ends"
    `$SLURM_SCRATCH` is **ephemeral** — Slurm wipes it as soon as the job
    finishes or is cancelled. Anything you want to keep **must be copied back**
    to persistent storage (your submit directory or project space) before the
    job exits. The `trap` in the examples below does exactly this.

Contrast this with your persistent storage — home (`ihome`) and project space —
which survives between jobs but is slower over the network. See
[**File Systems**](../data-management/file-systems.md) for the full storage
layout and quotas.

## When to use it

Scratch pays off when a job does heavy or repeated I/O: many small reads/writes,
large intermediate files, or random access patterns. For jobs that read an input
once and write a small result, the shared filesystem is fine and staging to
scratch just adds complexity.

## Single-node jobs: copy in, run, copy out

The pattern is: copy inputs into `$SLURM_SCRATCH`, run there, and copy results
back. This is the standard batch template from [Batch Jobs](batch-jobs.md) with
the scratch-handling lines added:

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

cp <inputs> $SLURM_SCRATCH          # 1. stage inputs onto local scratch
cd $SLURM_SCRATCH                   # 2. work there
run_on_exit(){ cp -r $SLURM_SCRATCH/* $SLURM_SUBMIT_DIR; }
trap run_on_exit EXIT               # 3. copy everything back, even on early exit

srun <job executable with parameters>

crc-job-stats

cp <outputs> $SLURM_SUBMIT_DIR      # 4. copy the results you want to keep
```

The `trap ... EXIT` is the safety net: it runs `run_on_exit` whenever the script
exits — including if the job is cancelled or times out — so intermediate results
on scratch are copied back to `$SLURM_SUBMIT_DIR` (the directory you submitted
from) rather than lost.

## Multi-node jobs: `sbcast` and `sgather`

`$SLURM_SCRATCH` is **local to each node**, so on a multi-node job every node has
its own separate scratch. Use `sbcast` to push a file out to every node's
scratch before the run, and `sgather` to collect per-node files back afterward:

```bash
#!/bin/bash
#SBATCH --output=job.o%j
#SBATCH --error=job.e%j
#SBATCH --job-name="TestScratch"
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=48
#SBATCH --cluster=mpi
#SBATCH --partition=mpi
#SBATCH --account=sam
#SBATCH --time=00:09:30

module purge

RUNDIR=/ix1/sam/chx33/simulations
cd $RUNDIR

echo 'Project storage path:' $PWD >> test.in

sbcast test.file $SLURM_SCRATCH/test.in

srun -n $SLURM_NNODES -N $SLURM_NNODES bash -c 'echo $(hostname):$SLURM_JOB_ID:$SLURM_TASK_PID Local node scratch:$SLURM_SCRATCH >> $SLURM_SCRATCH/scratch.out'
srun -n $SLURM_NNODES -N $SLURM_NNODES bash -c 'cat test.in >> $SLURM_SCRATCH/scratch.out'

sgather $SLURM_SCRATCH/scratch.out $RUNDIR/copy_scratch

files=($(ls $RUNDIR/copy*))
for file in ${files[@]}; do echo "Filename: $file"; echo "File content:"; tail $file; done
```

`sgather` appends each node's hostname to the gathered filename, so you can see
which node produced which output:

```
[chx33@login1 simulations]$ sbatch mpiscratch.slurm
Submitted batch job 2162366 on cluster mpi
[chx33@login1 simulations]$ tail job.o2162366
Filename: /ix1/sam/chx33/simulations/copy_scratch.mpi-n62.crc.pitt.edu
File content:
mpi-n62.crc.pitt.edu:2162366:20097 Local node scratch:/scratch/slurm-2162366
Project storage path: /ix1/sam/chx33/simulations
Filename: /ix1/sam/chx33/simulations/copy_scratch.mpi-n63.crc.pitt.edu
File content:
mpi-n63.crc.pitt.edu:2162366:30979 Local node scratch:/scratch/slurm-2162366
Project storage path: /ix1/sam/chx33/simulations
```

## Related

<div class="grid cards" markdown>

-   :material-file-document-edit:{ .lg .middle } __Batch job basics__

    ---

    The full submission script this scratch pattern builds on.

    [:octicons-arrow-right-24: Batch Jobs](batch-jobs.md)

-   :material-harddisk:{ .lg .middle } __Where your data lives__

    ---

    Home, project space, and quotas on the shared filesystems.

    [:octicons-arrow-right-24: File Systems](../data-management/file-systems.md)

-   :material-tools:{ .lg .middle } __Job statistics__

    ---

    `crc-job-stats` and other CRC helper commands.

    [:octicons-arrow-right-24: CRC Wrappers](../applications/crc-wrappers.md)

</div>
