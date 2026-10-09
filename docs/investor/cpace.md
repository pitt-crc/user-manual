---
title: cpace
---

# CPACE Investor Partition (GPU Cluster)

The `cpace` partition gives members of the CPACE group **on-demand, priority access** to a
dedicated 8× NVIDIA H200 node on the GPU cluster, purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no CPACE member is using it, the node is lent to the broader Pitt community through the
GPU cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so it
never sits idle. When you need it, you simply submit to `cpace`: Slurm immediately removes whatever community
`preempt` jobs are in the way and starts your job. You don't need to ask anyone, file a ticket,
or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    Only members of the `cpace` Unix group. To check your membership, run:

    ```
    id -Gn | tr ' ' '\n' | grep -x cpace
    ```

    If this prints `cpace`, you have access. If it prints nothing, ask your PI to request that
    CRCD add you to the group.

    You also need to be associated with the `cpace` Slurm account on the GPU cluster. Check with:

    ```
    sacctmgr show assoc where cluster=gpu account=cpace user=$USER format=Cluster,Account,User,QOS%60
    ```

    If this returns no rows, contact CRCD and ask to be added to the `cpace` account.

!!! danger "Always submit with `--account=cpace`"
    Every `cpace` job must charge the `cpace` Slurm account. Many CPACE members have a different
    *default* account (for example, their PI's research group), and the CPACE QoS levels are
    attached only to the `cpace` account. A job sent to the `cpace` partition under any other
    account will be rejected or will never start.

    Add `--account=cpace` (or `-A cpace`) to every `cpace` job, as all the examples below do.
    Don't change your default account to `cpace`: that would also change which account is
    charged for your jobs on SMP, HTC, MPI, and the other GPU partitions.

## The hardware

| Partition | Nodes | GPU         | VRAM   | GPU/Node | `--constraint`       | CPU  | Cores/Node | Mem/Node | Node Name |
| --------- | ----- | ----------- | ------ | -------- | -------------------- | ---- | ---------- | -------- | --------- |
| cpace     | 1     | NVIDIA H200 | 141 GB | 8        | h200,141g,amd,cpace  | AMD  | 128        | ~1.5 TB  | gpu-n91   |

Every GPU you request comes with a default share of the node's CPUs and memory, so a request for
GPUs alone gets you a sensible, balanced allocation:

| GPUs requested (`--gres=gpu:N`) | Default cores | Default memory |
| ------------------------------- | ------------- | -------------- |
| 1                               | 16            | 192 GB         |
| 2                               | 32            | 384 GB         |
| 4                               | 64            | 768 GB         |
| 8 (whole node)                  | 128           | 1,536 GB       |

These defaults come from the partition settings of 16 cores per GPU and 12 GB of memory per core.
You can override them with `--cpus-per-task` and `--mem`. Unlike the `a100`, `rtx6k`, and `h200`
partitions, `cpace` has **no limit on cores per GPU**. You can pair one GPU with more than 16
cores, up to the node's 128 cores. Keep in mind that cores you hold can stop the node's remaining
GPUs from being used by anyone else.

!!! note "Every job must request a GPU"
    Include `--gres=gpu:N` (N = 1 to 8) in every `cpace` job, even jobs that are mostly CPU work.
    Use the plain form `gpu:N`, without a GPU type name such as `gpu:h200:4`; the submit filter
    rejects requests in that form.

!!! success "No Service Unit cost"
    The `cpace` partition has a GPU billing weight of `0`. Jobs here do **not** draw down your
    group's [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

The same node, gpu-n91, belongs to two partitions at once:

|                            | `cpace` (your partition)                 | `preempt` (community)                             |
| -------------------------- | ---------------------------------------- | ------------------------------------------------- |
| Who can submit             | CPACE group members only                 | Anyone with a GPU cluster account                 |
| SU cost                    | 0                                        | 0                                                 |
| Can be preempted?          | **No**                                   | **Yes**, stopped immediately when CPACE needs it  |
| Scheduling priority        | Investor (highest)                       | Lowest                                            |

When you submit a job to `cpace`, this is what happens:

1. Slurm checks whether gpu-n91 has enough free GPUs, cores, and memory for your request.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. Those jobs are either requeued or cancelled, depending on how they were
   submitted. Either way, they leave the node at once, because CPACE preemption has no grace period.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    CPACE members. If your group is already using all 8 GPUs, your job waits in the queue like any
    normal job until one of those jobs finishes.

    There is **no per-person or per-group GPU cap** on the CPACE QoS levels. A single member can
    occupy all 8 GPUs for up to 6 days. Please coordinate large or long jobs within your group,
    and ask for only the GPUs and walltime you actually need.

    Like all cluster hardware, gpu-n91 is also unavailable during cluster-wide maintenance.

## Step 1: See what is on the node (optional)

You never need to check before submitting, but it helps to know what to expect. To see how many
of the node's GPUs are in use:

```
sinfo -M gpu -p cpace -N -O "NodeList:12,StateCompact:10,Gres:20,GresUsed:30"
```

To see which jobs are on the node and which partition they came from:

```
squeue -M gpu -w gpu-n91 -o "%.12i %.10P %.10u %.3t %.12M %b"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `cpace` belong to your group and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab GPUs for testing, debugging, or exploratory work.
See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 1 H200, 16 cores, and 192 GB of memory for 4 hours:

```
crc-interactive -g -p cpace -a cpace -u 1 -c 16 -b 192 -t 4
```

| Flag     | Meaning                              |
| -------- | ------------------------------------ |
| `-g`     | use the GPU cluster                  |
| `-p`     | partition, which must be `cpace`     |
| `-a`     | Slurm account, which must be `cpace` |
| `-u`     | number of GPUs (1 to 8)              |
| `-c`     | number of cores                      |
| `-b`     | memory in GB                         |
| `-t`     | walltime in hours, or `hours:minutes`|

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` supplies its own small defaults for cores and memory, which can override the
    partition's per-GPU defaults. Give `-c` and `-b` explicitly (16 cores and 192 GB per GPU is a
    good starting point). Add `-z` to print the equivalent `srun` command without running it.

### With `srun`

The equivalent direct Slurm command is:

```
srun -M gpu --partition=cpace --account=cpace --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=16 --mem=192G --time=04:00:00 --pty bash
```

When your prompt changes to `gpu-n91`, you are on the node. Confirm your GPUs with:

```
nvidia-smi
```

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.
    GPUs you hold but don't use are unavailable both to your group and to the community.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example requests 4 H200 GPUs for 1 day:

```bash
#!/bin/bash
#SBATCH --job-name=cpace-job
#SBATCH --cluster=gpu
#SBATCH --partition=cpace
#SBATCH --account=cpace
#SBATCH --nodes=1
#SBATCH --gres=gpu:4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=64      # optional: matches the default of 16 cores per GPU
#SBATCH --mem=768G              # optional: matches the default of 192 GB per GPU
#SBATCH --time=1-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>      # e.g. a CUDA or Python environment

nvidia-smi
python train.py

crc-job-stats
```

Submit it from a login node with:

```
sbatch cpace-job.slurm
```

To use the entire node, request `--gres=gpu:8`. With the default per-GPU allocation, this gives
you all 128 cores and roughly 1.5 TB of memory.

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `cpace` jobs cannot be preempted by community users, so you don't
    need `--requeue` or preemption-proof checkpointing. Checkpointing long runs is still good
    practice in case of node failure or maintenance.

## Walltime and QoS

`cpace` jobs follow the same walltime tiers as the rest of the GPU cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the CPACE investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching CPACE QoS automatically:

| Your `--time`               | Assigned QoS   |
| --------------------------- | -------------- |
| up to 1-00:00:00 (1 day)    | `gpu-cpace-s`  |
| up to 3-00:00:00 (3 days)   | `gpu-cpace-n`  |
| up to 6-00:00:00 (6 days)   | `gpu-cpace-l`  |
| more than 6 days            | rejected       |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the CPACE QoS levels have no GPU, CPU, memory, or job-count limits. The only
limit is the node itself (8 GPUs, 128 cores, about 1.5 TB of memory). All three tiers can preempt
community `preempt` jobs.

To see the current CPACE QoS settings, run:

```
sacctmgr show qos where name=gpu-cpace-s,gpu-cpace-n,gpu-cpace-l format=Name,MaxWall,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `cpace`

Check your jobs on the GPU cluster:

```
squeue -M gpu -u $USER
```

To see every job in the CPACE group, which is useful when the node is busy:

```
squeue -M gpu -A cpace
```

Check one job's details:

```
scontrol -M gpu show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=cpace`, `Partition=cpace`, a `gpu-cpace-*` QoS, and
`NodeList=gpu-n91`. If the account is anything other than `cpace`, you left out
`--account=cpace`. If you see `preempt`, `h200`, or `a100` instead of `cpace`, you left out
`--partition=cpace`. Jobs submitted to
`preempt` can be preempted at any time, and jobs on `h200` or `a100` are charged Service Units.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `User's group not permitted to use this partition` | You are not in the `cpace` group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| `Invalid qos specification` | You submitted under your default account instead of `cpace`. | Add `--account=cpace` (or `-a cpace` for `crc-interactive`) and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the `cpace` Slurm account yet. | Run the `sacctmgr show assoc` check above. If it returns nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w gpu-n91` shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, node full of `cpace` jobs | Your group is using the node. | Wait, request fewer GPUs, or coordinate within your group. |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | The node is down, drained, or reserved for maintenance. | Check `sinfo -M gpu -p cpace` and watch for CRCD maintenance announcements. |
| `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days, the maximum for `cpace`. | Request `--time=6-00:00:00` or less, and checkpoint longer runs. |
| `ERROR: You must request a GPU (--gres=gpu:N) to run on this cluster!` | The job has no `--gres` request. Requesting GPUs with `--gpus` or `--gpus-per-node` can also cause this. | Add `--gres=gpu:N` (or `-u N` for `crc-interactive`). |
| `ERROR: You must request at least as many GPUs as nodes.` | The GPU request includes a type name (for example `--gres=gpu:h200:4`) or asks for 0 GPUs. | Use the plain form `--gres=gpu:N` with N of 1 or more. The partition already guarantees H200s. |
| Job rejected for requesting too many CPUs | More than 128 cores requested on the node. | Request at most 128 cores in total (`--ntasks-per-node` × `--cpus-per-task`). |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`, for example `-b 192` per GPU. |

## Related

- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of this node works.
- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [GPU Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
  describes the rest of the GPU cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
