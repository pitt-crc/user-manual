---
title: lchong 
---

# lchong Investor Partition (GPU Cluster)

The `lchong` partition gives members of the lchong group **on-demand, priority access** to three
dedicated GPU nodes on the GPU cluster, each with 8× NVIDIA RTX PRO 6000 Blackwell GPUs (24 GPUs
in total). The nodes were purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no lchong member is using them, the nodes are lent to the broader Pitt community through the
GPU cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so
they never sit idle. When you need them, you simply submit to `lchong`: Slurm immediately removes
whatever community `preempt` jobs are in the way and starts your job. You don't need to ask
anyone, file a ticket, or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    Only members of the `lchong` Unix group. To check your membership, run:

    ```
    id -Gn | tr ' ' '\n' | grep -x lchong
    ```

    If this prints `lchong`, you have access. If it prints nothing, ask your PI to request that
    CRCD add you to the group.

    You also need to be associated with the `lchong` Slurm account on the GPU cluster. Check with:

    ```
    sacctmgr show assoc where cluster=gpu account=lchong user=$USER format=Cluster,Account,User
    ```

    If this returns no rows, contact CRCD and ask to be added to the `lchong` account.

!!! danger "Always submit with `--account=lchong`"
    Every `lchong` job must charge the `lchong` Slurm account, because the lchong QoS levels are
    attached to that account. Your *default* account may be a different group, for example if you
    also belong to another lab. A job sent to the `lchong` partition under any other account will
    be rejected.

    Default accounts are set **per cluster**, so check the GPU one specifically. The `Def Acct`
    column shows your GPU default:

    ```
    sacctmgr show user $USER withassoc where cluster=gpu format=Cluster,User,DefaultAccount,Account
    ```

    Add `--account=lchong` (or `-A lchong`) to every `lchong` job, as all the examples below do. If
    `lchong` is already your default account, the flag is harmless. Don't change your default
    account to `lchong` just for this, because that would also change which account is charged for
    your jobs on other clusters and partitions.

## The hardware

| Partition | Nodes | GPU                           | VRAM  | GPU/Node | `--constraint` | CPU           | Cores/Node | Mem/Node | Scratch     | Network   | Node Names   |
| --------- | ----- | ----------------------------- | ----- | -------- | -------------- | ------------- | ---------- | -------- | ----------- | --------- | ------------ |
| lchong    | 3     | NVIDIA RTX PRO 6000 Blackwell | 96 GB | 8        | rtx6k,96g,amd  | AMD EPYC 9555 | 128        | 1.5 TB   | 7.2 TB NVMe | HDR200 IB | gpu-n[83-85] |

These nodes are the same model as the community
[`rtx6k` partition](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/), so code and
environments that work on `rtx6k` work here unchanged.

Every GPU you request comes with a default share of the node's CPUs and memory, so a request for
GPUs alone gets you a sensible, balanced allocation:

| GPUs per node (`--gres=gpu:N`) | Default cores | Default memory |
| ------------------------------ | ------------- | -------------- |
| 1                              | 16            | 192 GB         |
| 2                              | 32            | 384 GB         |
| 4                              | 64            | 768 GB         |
| 8 (whole node)                 | 128           | 1,536 GB       |

These defaults come from the partition settings of 16 cores per GPU and 12 GB of memory per core.
You can override them with `--cpus-per-task` and `--mem`, up to the node's 128 cores and about
1.5 TB. As an investment partition, `lchong` has **no limit on cores per GPU**, so you can pair a
single GPU with more than 16 cores. Keep in mind that cores you hold can stop the node's remaining
GPUs from being used by anyone else.

!!! note "Every job must request a GPU"
    Include `--gres=gpu:N` (N = 1 to 8 per node) in every `lchong` job, even jobs that are mostly
    CPU work. Use the plain form `gpu:N`, without a type name such as `gpu:rtx6k:4`. The GPUs on
    these nodes aren't typed in Slurm, so a typed request is rejected.

!!! success "No Service Unit cost"
    The `lchong` partition has a GPU billing weight of `0`. Jobs here do **not** draw down your
    group's [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

The same three nodes, gpu-n[83-85], belong to two partitions at once:

|                     | `lchong` (your partition) | `preempt` (community)                             |
| ------------------- | ------------------------- | ------------------------------------------------- |
| Who can submit      | lchong group members only | Anyone with a GPU cluster account                 |
| SU cost             | 0                         | 0                                                 |
| Can be preempted?   | **No**                    | **Yes**, stopped immediately when lchong needs it |
| Scheduling priority | Investor (highest)        | Lowest                                            |

When you submit a job to `lchong`, this is what happens:

1. Slurm looks for room on gpu-n[83-85] for your request: free GPUs, cores, and memory on as many
   nodes as you asked for.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. There is no grace period: those jobs are killed immediately and lose any
   work in progress. Batch jobs are normally put back in the queue, but they start over from the
   beginning; interactive sessions simply end. Either way, the nodes are freed for you at once.
3. Your job starts as soon as the nodes finish cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    lchong members. If your group is already using the GPUs you need, your job waits in the queue
    like any normal job until enough of those jobs finish.

    There is **no per-person or per-group GPU cap** on the lchong QoS levels. A single member can
    occupy all 24 GPUs for up to 6 days. Please coordinate large or long jobs within your group,
    and ask for only the GPUs, nodes, and walltime you actually need. When all 24 lchong GPUs are
    busy, you can also run on the community `rtx6k` partition, which has the same hardware but
    charges Service Units to your normal account.

    Like all cluster hardware, the lchong nodes are also unavailable during cluster-wide
    maintenance.

## Step 1: See what is on the nodes (optional)

You never need to check before submitting, but it helps to know what to expect. To see how many
GPUs are in use on each node:

```
sinfo -M gpu -p lchong -N -O "NodeList:14,StateCompact:10,Gres:20,GresUsed:30"
```

To see which jobs are on the nodes and which partition they came from:

```
squeue -M gpu -w gpu-n[83-85] -o "%.12i %.10P %.10u %.3t %.12M %.6D %b"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `lchong` belong to your group and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab GPUs for testing, debugging, or exploratory work.
See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 1 GPU, 16 cores, and 192 GB of memory for 4 hours:

```
crc-interactive -g -p lchong -a lchong -u 1 -c 16 -b 192 -t 4
```

| Flag | Meaning                               |
| ---- | ------------------------------------- |
| `-g` | use the GPU cluster                   |
| `-p` | partition, which must be `lchong`     |
| `-a` | Slurm account, which must be `lchong` |
| `-u` | number of GPUs (1 to 8)               |
| `-c` | number of cores                       |
| `-b` | memory in GB                          |
| `-t` | walltime in hours, or `hours:minutes` |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` supplies its own small defaults for cores and memory, which can override the
    partition's per-GPU defaults. Give `-c` and `-b` explicitly (16 cores and 192 GB per GPU is a
    good starting point). Add `-z` to print the equivalent `srun` command without running it.

### With `srun`

The equivalent direct Slurm command is:

```
srun -M gpu --partition=lchong --account=lchong --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=16 --mem=192G --time=04:00:00 --pty bash
```

When your prompt changes to `gpu-n83`, `gpu-n84`, or `gpu-n85`, you are on an lchong node.
Confirm your GPUs with:

```
nvidia-smi
```

!!! note "Release the nodes when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.
    GPUs you hold but don't use are unavailable both to your group and to the community.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example requests 4 GPUs on one node for
1 day:

```bash
#!/bin/bash
#SBATCH --job-name=lchong-job
#SBATCH --cluster=gpu
#SBATCH --partition=lchong
#SBATCH --account=lchong
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
sbatch lchong-job.slurm
```

To use an entire node, request `--gres=gpu:8`. With the default per-GPU allocation, this gives
you all 128 cores and roughly 1.5 TB of memory on that node.

### Multi-node jobs

The three lchong nodes are connected by HDR200 InfiniBand, and a single job can span up to all
three nodes (24 GPUs). Note that `--gres=gpu:N` is **per node**. This example runs on 2 full
nodes, with one task per GPU:

```bash
#!/bin/bash
#SBATCH --job-name=lchong-multinode
#SBATCH --cluster=gpu
#SBATCH --partition=lchong
#SBATCH --account=lchong
#SBATCH --nodes=2
#SBATCH --gres=gpu:8            # 8 GPUs on EACH node = 16 GPUs total
#SBATCH --ntasks-per-node=8     # one task per GPU
#SBATCH --cpus-per-task=16
#SBATCH --time=1-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>

srun python train_distributed.py
```

How the tasks find each other depends on your framework, for example PyTorch distributed reading
Slurm's environment variables, or a `torchrun` launcher started once per node. A multi-node job
starts only when every node it needs has room, so it is more likely to wait behind your group's
own jobs than a single-node job.

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `lchong` jobs cannot be preempted by community users, so you don't
    need `--requeue` or preemption-proof checkpointing. Checkpointing long runs is still good
    practice in case of node failure or maintenance.

## Walltime and QoS

`lchong` jobs follow the same walltime tiers as the rest of the GPU cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the lchong investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching lchong QoS automatically:

| Your `--time`             | Assigned QoS    |
| ------------------------- | --------------- |
| up to 1-00:00:00 (1 day)  | `gpu-lchong-s`  |
| up to 3-00:00:00 (3 days) | `gpu-lchong-n`  |
| up to 6-00:00:00 (6 days) | `gpu-lchong-l`  |
| more than 6 days          | rejected        |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the lchong QoS levels have no GPU, CPU, memory, or job-count limits. The
only limit is the hardware itself (3 nodes, 24 GPUs). All three tiers can preempt community
`preempt` jobs.

To see the current lchong QoS settings, run:

```
sacctmgr show qos where name=gpu-lchong-s,gpu-lchong-n,gpu-lchong-l format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `lchong`

Check your jobs on the GPU cluster:

```
squeue -M gpu -u $USER
```

To see every job in the lchong group, which is useful when the nodes are busy:

```
squeue -M gpu -A lchong
```

Check one job's details:

```
scontrol -M gpu show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=lchong`, `Partition=lchong`, a `gpu-lchong-*` QoS, and a
`NodeList` within gpu-n[83-85]. If the account is anything other than `lchong`, you left out
`--account=lchong`. If you see `preempt`, `rtx6k`, or `a100` instead of `lchong`, you left out
`--partition=lchong`. Jobs submitted to `preempt` can be preempted at any time, and jobs on
`rtx6k` or `a100` are charged Service Units.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `User's group not permitted to use this partition` | You are not in the `lchong` group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| `Invalid qos specification` | You submitted under a different account instead of `lchong`. | Add `--account=lchong` (or `-a lchong` for `crc-interactive`) and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the `lchong` Slurm account yet. | Run the `sacctmgr show assoc` check above. If it returns nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w gpu-n[83-85]` shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, nodes full of `lchong` jobs | Your group is using the GPUs you need. | Wait, request fewer GPUs or nodes, coordinate within your group, or run on `rtx6k` (charged SUs). |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M gpu -p lchong` and watch for CRCD maintenance announcements. A multi-node job may fit if you request fewer nodes. |
| `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days, the maximum for `lchong`. | Request `--time=6-00:00:00` or less, and checkpoint longer runs. |
| `ERROR: You must request a GPU (--gres=gpu:N) to run on this cluster!` | The job has no `--gres` request. Requesting GPUs with `--gpus` or `--gpus-per-node` can also cause this. | Add `--gres=gpu:N` (or `-u N` for `crc-interactive`). |
| `Invalid generic resource (gres) specification` | The GPU request includes a type name, for example `--gres=gpu:rtx6k:4`, or asks for more than 8 GPUs per node. | Use the plain form `--gres=gpu:N` with N from 1 to 8. The partition already guarantees RTX PRO 6000 GPUs. |
| Job rejected for requesting too many CPUs | More than 128 cores requested on a node. | Request at most 128 cores per node (`--ntasks-per-node` × `--cpus-per-task`). |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`, for example `-b 192` per GPU. |

## Related

- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of these nodes works.
- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [GPU Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
  describes the rest of the GPU cluster, including the community `rtx6k` partition.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
