---
title: Preemptible Partitions
---

# Preemptible Partitions

## What the preempt partitions are

Each cluster has a `preempt` partition that lets you use **idle** resources at **no Service Unit
cost**. In exchange, your jobs are **preemptible**: they run only on resources that would
otherwise sit idle, and Slurm stops them as soon as **any regular or investor job** needs those
resources.

| Cluster | Partition     | Nodes                                       | Job shape            | Max walltime |
| ------- | ------------- | ------------------------------------------- | -------------------- | ------------ |
| GPU     | `preempt`     | gpu-n[45-91], mems-n0                       | GPU jobs             | 6 days       |
| SMP     | `preempt`     | nearly all SMP nodes, including `high-mem`  | 1 node               | 21 days      |
| HTC     | `preempt`     | htc-n[24-93], htc-1024-n[0-3]               | 1 node               | 21 days      |
| MPI     | `preempt`     | mpi-n[0-135]                                | **at least 2 nodes** | 6 days       |
| MPI     | `preempt_ndr` | mpi-n[136-153]                              | 1 or more nodes      | 6 days       |

- **Zero cost.** The `preempt` partitions have Service Unit billing weights of `0` (see
  [Service Units](https://crc-pages.pitt.edu/user-manual/slurm/service-units/)), and they aren't
  subject to the per-group CPU, memory, and GPU caps of the regular partitions.
- **Shared hardware.** The preempt nodes are the same nodes used by the regular and investor
  partitions on each cluster. Your job borrows them while no one with higher priority needs them.
- **Preemptible.** When your job is preempted, it is stopped immediately and **loses any work in
  progress**. Batch jobs then go back into the queue and **start over from the beginning**. See
  [What happens when your job is preempted](#what-happens-when-your-job-is-preempted).

## What happens when your job is preempted

Preemption is QoS-based (`PreemptType=preempt/qos`). When a regular or investor job needs
resources your preempt job holds, Slurm does the following:

1. **Warns your job, briefly.** Slurm sends your job's processes `SIGTERM` at the moment of
   preemption. There is no grace period, so the job has exactly **30 seconds** before it is
   force-killed with `SIGKILL`. That's enough for a program that catches `SIGTERM` to write a
   quick final checkpoint, but not enough for anything more.
2. **Kills the job.** Everything not already saved to disk is lost: results held in memory,
   partially written output, and the remaining work.
3. **Requeues or ends it**, depending on the kind of job. This is the same on every cluster:

| Kind of job                                 | After preemption                                                              |
| ------------------------------------------- | ----------------------------------------------------------------------------- |
| Batch job (`sbatch`), the default           | **Requeued.** It returns to the queue and later reruns **from the beginning**. |
| Batch job submitted with `--no-requeue`     | **Cancelled.** It stays ended; resubmit it yourself if needed.                 |
| Interactive session (`srun`, `salloc`, `crc-interactive`) | **Ended.** Interactive sessions can't be requeued.                |

!!! warning "Requeued does not mean resumed"
    A requeued job doesn't pick up where it stopped. Slurm simply runs your job script again from
    the first line. Unless your program saves checkpoints and knows how to resume from them, every
    preemption throws away all the work the job had done.

### Side effects of requeueing

A requeued job keeps its **job ID** and runs your script again. That has a few consequences to
plan for:

- **Your output file is overwritten.** By default, each run truncates the `--output` and `--error`
  files, so the log from the preempted attempt disappears. Add `--open-mode=append` to keep the
  logs from every attempt in one file.
- **Your script can tell it's a rerun.** Slurm sets `SLURM_RESTART_COUNT` to the number of times
  the job has been requeued. It's unset or `0` on the first run. Use it to log attempts or to
  decide whether to resume from a checkpoint.
- **It may run on a different node.** Anything your job left in node-local `/scratch` is gone.
  Write checkpoints you need to keep to shared storage, such as your project or home directory.
- **It keeps its original request.** If you pinned the job to a node with `--nodelist` (`-w`),
  the requeued job waits for that same node, which was just reclaimed by someone else. It shows
  as pending with a reason like `ReqNodeNotAvail, May be reserved for other job`. Avoid pinning
  preempt jobs to specific nodes.
- **It can be preempted again.** A requeued job waits in the queue like a new job and can be
  preempted every time it runs. Without checkpointing, a long job may never finish.
- **Files from the earlier attempt are still there.** If your script appends to result files or
  creates files that the next run doesn't expect, a rerun can duplicate or corrupt results.
  Make your scripts safe to run twice, or use `--no-requeue`.

When a job is preempted, Slurm writes a line like this to its output file. It says `CANCELLED`
even when the job is then requeued:

```
slurmstepd: error: *** JOB 24301861 ON smp-n259 CANCELLED AT 2026-10-10T11:48:44 DUE TO PREEMPTION ***
```

To see whether the job was requeued, and each attempt it made, use `sacct` with `--duplicates`:

```
sacct -M <cluster> -j <jobid> --duplicates --format=JobID,State,Start,End,Elapsed,NodeList
```

For example, a batch job preempted once and then requeued looks like this:

```
JobID             State               Start                 End    Elapsed        NodeList
------------ ---------- ------------------- ------------------- ---------- ---------------
24301861      PREEMPTED 2026-10-10T11:43:01 2026-10-10T11:48:44   00:05:43        smp-n259
24301861.ba+  CANCELLED 2026-10-10T11:43:01 2026-10-10T11:49:14   00:06:13        smp-n259
24301861.ex+  COMPLETED 2026-10-10T11:43:01 2026-10-10T11:48:44   00:05:43        smp-n259
24301861        PENDING             Unknown             Unknown   00:00:00   None assigned
```

The first three lines are the preempted attempt. It ended at 11:48:44, and its batch step was
force-killed 30 seconds later. The last line, with the same job ID, is the requeued job waiting
to run again from the beginning.

## Choosing how your job handles preemption

| Your workload                                                     | Recommended setup                                               |
| ----------------------------------------------------------------- | --------------------------------------------------------------- |
| Short jobs that are safe to run twice                             | Keep the default (requeue). A preempted job just reruns.        |
| Long jobs with checkpointing                                      | Keep the default, and add `--open-mode=append`. Each rerun resumes from the latest checkpoint. |
| Jobs that aren't safe to run twice, or that you'd rather resubmit yourself | Add `--no-requeue`, then resubmit failed jobs on your own terms. |
| Long jobs without checkpointing                                   | Use a regular partition instead. On `preempt`, such jobs may restart repeatedly and never finish. |

## Good use cases

Any preempt job must assume it can be stopped at any moment, with 30 seconds' notice.
Two patterns fit well:

**Many short, independent jobs.** Short jobs lose little work when preempted, and with the
default requeue they rerun automatically. This works well for
[job arrays](https://crc-pages.pitt.edu/user-manual/slurm/job-arrays/) where each task writes its
own output file, so a rerun simply replaces an incomplete result.

**Long, restartable jobs.** A long job should checkpoint: write its state to shared storage at
regular intervals, and on startup look for the latest checkpoint and resume from it. With the
default requeue, Slurm restarts the job after each preemption and it continues from its last
checkpoint, losing only the work since then.

## Submitting to a preempt partition

Request the partition on the cluster you want:

```
#SBATCH --cluster=<cluster>
#SBATCH --partition=preempt
```

Everything else works like a normal [batch job](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/)
or [interactive job](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/). The QoS is
chosen automatically from your `--time`, using the same walltime tiers as the regular partitions:
`short`, `normal`, and `long` on every cluster, plus `long-long` on SMP and HTC (see
[Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)).

### Example: a checkpointing job that survives preemption

```bash
#!/bin/bash
#SBATCH --job-name=preempt-ckpt
#SBATCH --cluster=smp
#SBATCH --partition=preempt
#SBATCH --nodes=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=64G
#SBATCH --time=3-00:00:00
#SBATCH --output=%x-%j.out
#SBATCH --open-mode=append      # keep the log from every attempt

echo "=== Attempt $(( ${SLURM_RESTART_COUNT:-0} + 1 )) on $(hostname) at $(date) ==="

CKPT_DIR=$HOME/myproject/checkpoints   # shared storage, not node-local /scratch
mkdir -p "$CKPT_DIR"

module purge
module load <your-modules>

# Your program should save its state to $CKPT_DIR periodically, and resume from the
# latest checkpoint there if one exists. Catching SIGTERM to write one last checkpoint
# helps, but it only has 30 seconds.
./my_program --checkpoint-dir "$CKPT_DIR"
```

`--requeue` isn't needed, because batch jobs are requeued by default. Adding it does no harm and
makes your intent explicit.

### Example: opting out of requeue

For work you'd rather resubmit yourself, for example because rerunning it would overwrite results
you want to inspect first:

```
#SBATCH --partition=preempt
#SBATCH --no-requeue
```

### Cluster-specific notes

!!! tip "Targeting a GPU type"
    The GPU `preempt` partition spans several GPU types, so you can't select a type by partition
    name. Request GPUs with `--gres=gpu:<count>` and, if you need a specific model, add
    `--constraint=<feature>`. See the
    [GPU cluster hardware profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
    for the defined constraints.

!!! note "MPI node counts"
    On the MPI cluster, `preempt` requires **at least 2 nodes**. For single-node jobs on MPI
    hardware, use `preempt_ndr`, which accepts 1 or more nodes.

## Who can preempt your job

On every cluster, your preempt job can be displaced by **any regular or investor job** that
needs the resources it holds:

| Cluster | Partitions whose jobs preempt yours                                                        |
| ------- | ------------------------------------------------------------------------------------------ |
| GPU     | `a100`, `a100_multi`, `a100_nvlink`, `a100_nvlink_multi`, `l40s`, `rtx6k`, `h200`, and the investor partitions |
| SMP     | `smp`, `high-mem`, and the investor partitions                                              |
| HTC     | `htc` and the investor partitions                                                           |
| MPI     | `mpi` and `ndr`                                                                             |

This applies at every walltime tier, short through long-long. Preempt jobs never preempt each
other. Nodes on busy partitions, and investor nodes whose owners are active, are where preempt
jobs are stopped most often.

To see which partitions share a node your job ran on, run:

```
sinfo -M <cluster> -N -n <nodename> -o "%N %P"
```

If you want to stay off specific nodes, for example a group's investor nodes during a period you
know they are busy, add `--exclude=<nodelist>` to your job.

## Limits on preempt jobs

The preempt partitions aren't subject to the per-group CPU, memory, and GPU caps of the regular
partitions, but they do have job-count limits:

| Cluster | Running jobs                   | Submitted jobs (running + pending) |
| ------- | ------------------------------ | ---------------------------------- |
| GPU     | 100 per user, 500 per group    | 1,000 per user, 1,000 per group    |
| SMP     | no limit                       | 1,000 per user                     |
| HTC     | no limit                       | 1,000 per user                     |
| MPI     | no limit                       | 1,000 per user                     |

To see the current limits on any cluster, run:

```
sacctmgr show qos format=Name%18,MaxWall,MaxJobsPU,MaxJobsPA,MaxSubmitPU,MaxSubmitPA | grep preempt
```

## Related

- [Service Units](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) explains why
  preempt jobs are free.
- [Job Scheduling Policy](https://crc-pages.pitt.edu/user-manual/policies/job-scheduling-policy/)
  covers fair-share, QoS, and why jobs pend or get preempted.
- [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) covers job-script
  directives.
- [Job Arrays](https://crc-pages.pitt.edu/user-manual/slurm/job-arrays/) covers running many
  short tasks, a good fit for preempt.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains the investor partitions that share the preempt nodes.
