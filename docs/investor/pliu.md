---
title: pliu 
---

# pliu Investor Partitions (GPU and SMP Clusters)

The pliu group has invested in dedicated hardware on **two** CRCD clusters, both under the
partition name `pliu`. Members get **on-demand, priority access** to it, under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/):

| Cluster | Partition | Nodes | What you get                                         | Max job size                       | Max walltime |
| ------- | --------- | ----- | ---------------------------------------------------- | ---------------------------------- | ------------ |
| `gpu`   | `pliu`    | 3     | 24× NVIDIA RTX PRO 6000 Blackwell (8 per node)       | up to 3 nodes / 24 GPUs            | 6 days       |
| `smp`   | `pliu`    | 15    | 2,432 CPU cores across AMD Rome, Genoa, and Turin    | 1 node (up to 256 cores, ~1.5 TB)  | 21 days      |

Because both partitions are called `pliu`, **the cluster flag decides which one you get**:
use `-M gpu` / `--cluster=gpu` for the GPU nodes and `-M smp` / `--cluster=smp` for the CPU nodes.

When no pliu member is using them, these nodes are lent to the broader Pitt community through
each cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so
they never sit idle. When you need them, you simply submit to `pliu`: Slurm immediately removes
whatever community `preempt` jobs are in the way and starts your job. You don't need to ask
anyone, file a ticket, or wait for other users' jobs to finish.

## Access

!!! info "Who can use these partitions"
    You must be associated with the `pliu` Slurm account on each cluster you want to use. To check
    both clusters at once, run:

    ```
    sacctmgr show assoc where account=pliu user=$USER format=Cluster,Account,User
    ```

    You should see at least one row for `gpu` and one for `smp`. If a cluster is missing, contact
    CRCD and ask to be added to the `pliu` account on that cluster.

    The GPU cluster additionally requires membership in the `pliu` Unix group:

    ```
    id -Gn | tr ' ' '\n' | grep -x pliu
    ```

    If this prints nothing, ask your PI to request that CRCD add you to the group.

!!! danger "Always submit with `--account=pliu`"
    Every `pliu` job, on either cluster, must charge the `pliu` Slurm account, because the pliu QoS
    levels are attached to that account. Your *default* account may be a different group, for
    example if you also belong to another lab. A job sent to a `pliu` partition under any other
    account will be rejected.

    Default accounts are set **per cluster**, so your GPU and SMP defaults can differ. The
    `Def Acct` column shows your default on each cluster:

    ```
    sacctmgr show user $USER withassoc where cluster=gpu,smp format=Cluster,User,DefaultAccount,Account
    ```

    Add `--account=pliu` (or `-A pliu`) to every `pliu` job, as all the examples below do. If `pliu`
    is already your default account, the flag is harmless. Don't change your default account to
    `pliu` just for this, because that would also change which account is charged for your jobs on
    other partitions.

!!! success "No Service Unit cost"
    Both `pliu` partitions have billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

On each cluster, every pliu node belongs to two partitions at once:

|                     | `pliu` (your partition)  | `preempt` (community)                            |
| ------------------- | ------------------------ | ------------------------------------------------ |
| Who can submit      | pliu members only        | Anyone with an account on that cluster           |
| SU cost             | 0                        | 0                                                |
| Can be preempted?   | **No**                   | **Yes**, stopped immediately when pliu needs it  |
| Scheduling priority | Investor (highest)       | Lowest                                           |

When you submit a job to `pliu`, this is what happens:

1. Slurm looks for room on the pliu nodes for your request (GPUs, cores, and memory).
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. There is no grace period: those jobs are killed immediately and lose any
   work in progress. Batch jobs are normally put back in the queue, but they start over from the
   beginning; interactive sessions simply end. Either way, the node is freed for you at once.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    pliu members. If your group is already using the resources you need, your job waits in the
    queue like any normal job until enough of those jobs finish.

    There is **no per-person or per-group cap** on the pliu QoS levels. A single member can
    occupy all 24 GPUs for up to 6 days, or every SMP pliu node for up to 21 days. Please
    coordinate large or long jobs within your group, and ask for only the resources and walltime
    you actually need. The pliu nodes are also unavailable during cluster-wide maintenance.

---

## GPU cluster

### Hardware

| Partition | Nodes | GPU                           | VRAM  | GPU/Node | `--constraint` | CPU           | Cores/Node | Mem/Node | Scratch     | Network   | Node Names   |
| --------- | ----- | ----------------------------- | ----- | -------- | -------------- | ------------- | ---------- | -------- | ----------- | --------- | ------------ |
| pliu      | 3     | NVIDIA RTX PRO 6000 Blackwell | 96 GB | 8        | rtx6k,96g,amd  | AMD EPYC 9555 | 128        | 1.5 TB   | 7.2 TB NVMe | HDR200 IB | gpu-n[86-88] |

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
1.5 TB. As an investment partition, GPU `pliu` has **no limit on cores per GPU**, so you can pair a
single GPU with more than 16 cores. Keep in mind that cores you hold can stop the node's remaining
GPUs from being used by anyone else.

!!! note "Every GPU job must request a GPU"
    Include `--gres=gpu:N` (N = 1 to 8 per node) in every GPU `pliu` job, even jobs that are mostly
    CPU work. For CPU-only work, use the SMP `pliu` partition instead. Use the plain form `gpu:N`,
    without a type name such as `gpu:rtx6k:4`. The GPUs on these nodes aren't typed in Slurm, so a
    typed request is rejected.

### See what is on the nodes (optional)

To see how many GPUs are in use on each node:

```
sinfo -M gpu -p pliu -N -O "NodeList:14,StateCompact:10,Gres:20,GresUsed:30"
```

To see which jobs are on the nodes and which partition they came from:

```
squeue -M gpu -w gpu-n[86-88] -o "%.12i %.10P %.10u %.3t %.12M %.6D %b"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `pliu` belong to your group and will not be.

### Interactive session

This requests 1 GPU, 16 cores, and 192 GB of memory for 4 hours:

```
crc-interactive -g -p pliu -a pliu -u 1 -c 16 -b 192 -t 4
```

The equivalent direct Slurm command is:

```
srun -M gpu --partition=pliu --account=pliu --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=16 --mem=192G --time=04:00:00 --pty bash
```

When your prompt changes to `gpu-n86`, `gpu-n87`, or `gpu-n88`, you are on a pliu node. Confirm
your GPUs with `nvidia-smi`.

### Batch job

This example requests 4 GPUs on one node for 1 day:

```bash
#!/bin/bash
#SBATCH --job-name=pliu-gpu
#SBATCH --cluster=gpu
#SBATCH --partition=pliu
#SBATCH --account=pliu
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

To use an entire node, request `--gres=gpu:8`. With the default per-GPU allocation, this gives
you all 128 cores and roughly 1.5 TB of memory on that node.

### Multi-node GPU jobs

The three GPU nodes are connected by HDR200 InfiniBand, and a single job can span up to all three
nodes (24 GPUs). Note that `--gres=gpu:N` is **per node**. This example runs on 2 full nodes, with
one task per GPU:

```bash
#!/bin/bash
#SBATCH --job-name=pliu-multinode
#SBATCH --cluster=gpu
#SBATCH --partition=pliu
#SBATCH --account=pliu
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

When all 24 pliu GPUs are busy, you can also run on the community `rtx6k` partition, which has the
same hardware but charges Service Units to your normal account.

### GPU walltime and QoS

**You don't choose the QoS yourself.** When you submit, the GPU cluster's submit filter reads your
`--time` and assigns the matching pliu QoS automatically:

| Your `--time`             | Assigned QoS  |
| ------------------------- | ------------- |
| up to 1-00:00:00 (1 day)  | `gpu-pliu-s`  |
| up to 3-00:00:00 (3 days) | `gpu-pliu-n`  |
| up to 6-00:00:00 (6 days) | `gpu-pliu-l`  |
| more than 6 days          | rejected      |

---

## SMP cluster

### Hardware

SMP `pliu` spans three generations of AMD nodes. Pick a generation with `--constraint`:

| Generation | Nodes | `--constraint` | CPU            | Cores/Node | Mem/Node | Mem/Core | Max `--mem` per job | Scratch     | Network | Node Names     |
| ---------- | ----- | -------------- | -------------- | ---------- | -------- | -------- | ------------------- | ----------- | ------- | -------------- |
| Turin      | 8     | amd,turin      | AMD EPYC 9755  | 256        | 1.5 TB   | 6 GB     | `1540000M`          | 3.2 TB NVMe | 10GbE   | smp-n[257-264] |
| Genoa      | 5     | amd,genoa      | AMD EPYC 9374F | 64         | 768 GB   | 12 GB    | `773000M`           | 3.2 TB NVMe | 10GbE   | smp-n[252-256] |
| Rome       | 2     | amd,rome       | AMD EPYC 7302  | 32         | 256 GB   | 8 GB     | `257000M`           | 960 GB NVMe | 10GbE   | smp-n[211-212] |

Jobs without a `--constraint` go to whichever node has room, and Slurm fills the Turin and Genoa
nodes before the Rome nodes. Request a generation explicitly when your code is tuned for it, or
when you need a whole node of a particular size.

!!! warning "Set `--mem` on SMP"
    If you don't specify memory, an SMP job gets the cluster default of about **4 GB per core**.
    That's less than these nodes provide per core, and a common cause of out-of-memory failures.
    Request what you need with `--mem` (per node) or `--mem-per-cpu`, up to the per-node maximums
    in the table above.

!!! note "Single-node jobs only"
    SMP `pliu` jobs are limited to **one node**. A job that asks for more nodes is rejected at
    submission with `ERROR: Maximum node count is 1!`. The largest single job is a whole Turin
    node: 256 cores and about 1.5 TB of memory. For multi-node CPU work, use the MPI cluster.

### See what is on the nodes (optional)

To see how busy each pliu node is:

```
sinfo -M smp -p pliu -N -O "NodeList:16,StateCompact:10,Features:12,CPUsState:20,AllocMem:12,Memory:12"
```

`CPUsState` shows allocated, idle, other, and total cores. To see which jobs are on the nodes and
which partition they came from:

```
squeue -M smp -w smp-n[211-212,252-264] -o "%.12i %.10P %.10u %.3t %.12M %.5C %N"
```

### Interactive session

This requests 8 cores and 32 GB of memory for 4 hours:

```
crc-interactive -s -p pliu -a pliu -c 8 -b 32 -t 4
```

The equivalent direct Slurm command is:

```
srun -M smp --partition=pliu --account=pliu --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=8 --mem=32G --time=04:00:00 --pty bash
```

As with the GPU cluster, always pass cores (`-c`) and memory (`-b`) to `crc-interactive`, because
its own defaults are small.

### Batch job

This example runs a 64-core multithreaded job with 500 GB of memory for 3 days:

```bash
#!/bin/bash
#SBATCH --job-name=pliu-smp
#SBATCH --cluster=smp
#SBATCH --partition=pliu
#SBATCH --account=pliu
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=64
#SBATCH --mem=500G
#SBATCH --time=3-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
./my_program

crc-job-stats
```

To take an entire Turin node, use `--constraint=turin`, `--cpus-per-task=256`, and
`--mem=1540000M`.

### SMP walltime and QoS

SMP adds a fourth, **long-long** tier. As on the GPU cluster, the QoS is assigned automatically from
your `--time`:

| Your `--time`               | Assigned QoS    |
| --------------------------- | --------------- |
| up to 1-00:00:00 (1 day)    | `smp-pliu-s`    |
| up to 3-00:00:00 (3 days)   | `smp-pliu-n`    |
| up to 6-00:00:00 (6 days)   | `smp-pliu-l`    |
| up to 21-00:00:00 (21 days) | `smp-pliu-ll`   |
| more than 21 days           | rejected        |

---

## Walltime tips (both clusters)

You don't need `--qos`; just set `--time` to what your job actually needs. Always give `--time`
explicitly. If you leave it out, a cluster default walltime is applied, which may not match your
job. Because your pliu jobs cannot be preempted by community users, you don't need `--requeue` or
preemption-proof checkpointing, though checkpointing long runs is still good practice in case of
node failure or maintenance.

Apart from walltime, the pliu QoS levels on both clusters have no GPU, CPU, memory, or job-count
limits; the only limit is the hardware itself. Every tier can preempt community `preempt` jobs on
its cluster.

To see the current pliu QoS settings on both clusters, run:

```
sacctmgr show qos where name=gpu-pliu-s,gpu-pliu-n,gpu-pliu-l,smp-pliu-s,smp-pliu-n,smp-pliu-l,smp-pliu-ll format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `pliu`

To see all your jobs, and all pliu group jobs, on both clusters:

```
squeue -M gpu,smp -u $USER
squeue -M gpu,smp -A pliu
```

Check one job's details (use `-M gpu` or `-M smp` to match the job):

```
scontrol -M smp show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=pliu`, `Partition=pliu`, a `gpu-pliu-*` or `smp-pliu-*`
QoS, and a `NodeList` on the pliu nodes. If the account is anything other than `pliu`, you left
out `--account=pliu`. If you see `preempt`, `rtx6k`, `smp`, or `a100` instead of `pliu`, you left
out `--partition=pliu`. Jobs submitted to `preempt` can be preempted at any time, and jobs on the
general partitions are charged Service Units.

## Troubleshooting

| Cluster | What you see | Likely cause | What to do |
| --- | --- | --- | --- |
| both | `Invalid qos specification` | You submitted under a different account instead of `pliu`. | Add `--account=pliu` (or `-a pliu` for `crc-interactive`) and resubmit. |
| both | `Invalid account or account/partition combination specified` | You aren't associated with the `pliu` account on that cluster. | Run the `sacctmgr show assoc` check under [Access](#access). If the cluster is missing, contact CRCD with your username. |
| both | Job `PD` with reason `Resources` while the node check shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| both | Job `PD` with reason `Resources` or `Priority`, nodes full of `pliu` jobs | Your group is using the resources you need. | Wait, request less, or coordinate within your group. |
| both | Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M <cluster> -p pliu` and watch for CRCD maintenance announcements. |
| GPU | `User's group not permitted to use this partition` | You are not in the `pliu` Unix group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| GPU | `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days. | Request `--time=6-00:00:00` or less, or move CPU-only work to SMP `pliu`, which allows 21 days. |
| GPU | `ERROR: You must request a GPU (--gres=gpu:N) to run on this cluster!` | The job has no `--gres` request. Requesting GPUs with `--gpus` or `--gpus-per-node` can also cause this. | Add `--gres=gpu:N` (or `-u N` for `crc-interactive`). For CPU-only work, use SMP `pliu`. |
| GPU | `Invalid generic resource (gres) specification` | The GPU request includes a type name, for example `--gres=gpu:rtx6k:4`, or asks for more than 8 GPUs per node. | Use the plain form `--gres=gpu:N` with N from 1 to 8. |
| GPU | Job rejected for requesting too many CPUs | More than 128 cores requested on a node. | Request at most 128 cores per node (`--ntasks-per-node` × `--cpus-per-task`). |
| SMP | `ERROR: Maximum walltime is 21 days!` | `--time` is longer than 21 days, the SMP maximum. | Request `--time=21-00:00:00` or less, and checkpoint longer runs. |
| SMP | `ERROR: Maximum node count is 1!` | The job asks for more than one node. | SMP `pliu` is single-node only. Use `--nodes=1`, or run multi-node work on the MPI cluster. |
| SMP | `Requested node configuration is not available` | No pliu node can satisfy the request, for example `--constraint=rome` with more than 32 cores, or more memory than the largest node has. | Compare your request with the SMP hardware table and adjust cores, memory, or `--constraint`. |
| SMP | Job killed with an out-of-memory error | The job used the default of about 4 GB per core. | Set `--mem` or `--mem-per-cpu` to what the job needs. |
| both | Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`. |

## Related

- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of these nodes works.
- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [GPU](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/) and
  [SMP](https://crc-pages.pitt.edu/user-manual/hardware_profiles/smp/) hardware profiles describe
  the rest of each cluster.
- [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/) lists the general
  walltime tiers.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
