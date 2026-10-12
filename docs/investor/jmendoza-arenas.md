---
title: jmendoza-arenas
---

# jmendoza-arenas Investor Partition (GPU Cluster)

The `jmendoza-arenas` partition gives members of the jmendoza-arenas group **on-demand, priority access** to a
dedicated GPU node on the GPU cluster with 10× NVIDIA A40 GPUs, purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no jmendoza-arenas member is using it, the node is lent to the broader Pitt community through the GPU
cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so it
never sits idle. When you need it, you simply submit to `jmendoza-arenas`: Slurm immediately removes whatever
community `preempt` jobs are in the way and starts your job. You don't need to ask anyone, file a
ticket, or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    Only members of the `jmendoza-arenas` Unix group. To check your membership, run:

    ```
    id -Gn | tr ' ' '\n' | grep -x jmendoza-arenas
    ```

    If this prints `jmendoza-arenas`, you have access. If it prints nothing, ask your PI to request that
    CRCD add you to the group.

    You also need to be associated with the `jmendoza-arenas` Slurm account on the GPU cluster. Check with:

    ```
    sacctmgr show assoc where cluster=gpu account=jmendoza-arenas user=$USER format=Cluster,Account,User
    ```

    If this returns no rows, contact CRCD and ask to be added to the `jmendoza-arenas` account.

!!! danger "Always submit with `--account=jmendoza-arenas`"
    Every `jmendoza-arenas` job must charge the `jmendoza-arenas` Slurm account, because the jmendoza-arenas QoS levels are attached
    to that account. Your *default* account may be a different group, for example if you also
    belong to another lab. A job sent to the `jmendoza-arenas` partition under any other account will be
    rejected.

    Add `--account=jmendoza-arenas` (or `-A jmendoza-arenas`) to every `jmendoza-arenas` job, as all the examples below do. If `jmendoza-arenas`
    is already your default account, the flag is harmless. Don't change your default account to
    `jmendoza-arenas` just for this, because that would also change which account is charged for your other
    GPU jobs.

    Default accounts are set **per cluster**, so check the GPU one specifically. The `Def Acct`
    column shows your GPU default:

    ```
    sacctmgr show user $USER withassoc where cluster=gpu format=Cluster,User,DefaultAccount,Account
    ```

## The hardware

| Partition | Nodes | GPU | VRAM | GPU/Node | `--constraint` | CPU | Cores/Node | Mem/Node | Max `--mem` per job | Scratch | Node Name |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| jmendoza-arenas | 1 | NVIDIA A40 | 48 GB | 10 | a40 | 2× Intel Xeon Gold 6258R | 56 | ~996 GB | `1019800M` | 11 TB | mems-n0 |

Every GPU you request comes with a default share of the node's CPUs and memory:

| GPUs requested (`--gres=gpu:N`) | Default cores | Default memory |
| --- | --- | --- |
| 1 | 5 | 85 GB |
| 2 | 10 | 170 GB |
| 5 | 25 | 425 GB |
| 10 (all GPUs) | 50 | 850 GB |

These defaults come from the partition settings of 5 cores per GPU and 17 GB of memory per core.
You can override them with `--cpus-per-task` and `--mem`, up to the node's 56 cores and
`1019800M`. There is no limit on cores per GPU, but cores you hold can stop the node's remaining
GPUs from being used by anyone else.

!!! note "Every job must request a GPU"
    Include `--gres=gpu:N` (N = 1 to 10) in every `jmendoza-arenas` job. Use the plain form `gpu:N`, without
    a type name such as `gpu:a40:2`.

!!! note "Single node"
    `jmendoza-arenas` is a single node, so jobs are limited to 10 GPUs and 56 cores.

!!! success "No Service Unit cost"
    The `jmendoza-arenas` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

The node mems-n0 belongs to two partitions at once:

| | `jmendoza-arenas` (your partition) | `preempt` (community) |
| --- | --- | --- |
| Who can submit | jmendoza-arenas group members only | Anyone with a GPU cluster account |
| SU cost | 0 | 0 |
| Can be preempted? | **No** | **Yes**, stopped immediately when jmendoza-arenas needs it |
| Scheduling priority | Investor (highest) | Lowest |

When you submit a job to `jmendoza-arenas`, this is what happens:

1. Slurm checks whether mems-n0 has enough free GPUs, cores, and memory for your request.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. Those jobs are killed and lose any work in progress; batch jobs are put
   back in the queue to start over later. Either way, the node is freed for you at once.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    jmendoza-arenas members. If your group is already using the GPUs you need, your job waits in the queue
    like any normal job until enough of those jobs finish.

    There is **no per-person or per-group cap** on the jmendoza-arenas QoS levels. A single member
    can occupy all 10 GPUs for up to 6 days. Please coordinate large or long jobs within your
    group, and ask for only the GPUs and walltime you actually need. Like all cluster hardware, mems-n0 is also unavailable during cluster-wide
    maintenance.

## Step 1: See what is on the node (optional)

To see how many of the node's 10 GPUs are in use:

```
sinfo -M gpu -p jmendoza-arenas -N -O "NodeList:12,StateCompact:10,Gres:20,GresUsed:30"
```

To see which jobs are on the node and which partition they came from:

```
squeue -M gpu -w mems-n0 -o "%.12i %.16P %.10u %.3t %.12M %b"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `jmendoza-arenas` belong to your group and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab GPUs for testing, debugging, or exploratory work.
See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 1 GPU, 5 cores, and 85 GB of memory for 4 hours:

```
crc-interactive -g -p jmendoza-arenas -a jmendoza-arenas -u 1 -c 5 -b 85 -t 4
```

| Flag | Meaning |
| --- | --- |
| `-g` | use the GPU cluster |
| `-p` | partition, which must be `jmendoza-arenas` |
| `-a` | Slurm account, which must be `jmendoza-arenas` |
| `-u` | number of GPUs |
| `-c` | number of cores (default: 1) |
| `-b` | memory in GB |
| `-t` | walltime in hours, or `hours:minutes` (default: 1 hour) |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` defaults to a single core and a small amount of memory, which can override
    the partition's per-GPU defaults. Give `-c` and `-b` explicitly. Add `-z` to print the
    equivalent `srun` command without running it.

### With `srun`

The equivalent direct Slurm command is:

```
srun -M gpu --partition=jmendoza-arenas --account=jmendoza-arenas --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=5 --mem=85G --time=04:00:00 --pty bash
```

When your prompt changes to `mems-n0`, you are on a jmendoza-arenas node. Confirm your GPUs with `nvidia-smi`.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example requests 4 GPUs on one node for
1 day:

```bash
#!/bin/bash
#SBATCH --job-name=jmendoza-arenas-job
#SBATCH --cluster=gpu
#SBATCH --partition=jmendoza-arenas
#SBATCH --account=jmendoza-arenas
#SBATCH --nodes=1
#SBATCH --gres=gpu:4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=20      # optional: matches the per-GPU default
#SBATCH --mem=340G               # optional: matches the per-GPU default
#SBATCH --time=1-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>

nvidia-smi
python train.py

crc-job-stats
```

Submit it from a login node with:

```
sbatch jmendoza-arenas-job.slurm
```

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `jmendoza-arenas` jobs cannot be preempted by community users, so you don't
    need preemption-proof checkpointing. Checkpointing long runs is still good practice in case of
    node failure or maintenance.

## Walltime and QoS

`jmendoza-arenas` jobs follow the same walltime tiers as the rest of the GPU cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the jmendoza-arenas investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching QoS automatically:

| Your `--time` | Assigned QoS |
| --- | --- |
| up to 1-00:00:00 (1 day) | `gpu-jmendoza-arenas-s` |
| up to 3-00:00:00 (3 days) | `gpu-jmendoza-arenas-n` |
| up to 6-00:00:00 (6 days) | `gpu-jmendoza-arenas-l` |
| more than 6 days | rejected |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the jmendoza-arenas QoS levels have no GPU, CPU, memory, or job-count
limits. The only limit is the node itself. All three tiers can preempt community `preempt` jobs.

To see the current jmendoza-arenas QoS settings, run:

```
sacctmgr show qos where name=gpu-jmendoza-arenas-s,gpu-jmendoza-arenas-n,gpu-jmendoza-arenas-l format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `jmendoza-arenas`

Check your jobs on the GPU cluster, and every job in the jmendoza-arenas group:

```
squeue -M gpu -u $USER
squeue -M gpu -A jmendoza-arenas
```

Check one job's details:

```
scontrol -M gpu show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=jmendoza-arenas`, `Partition=jmendoza-arenas`, a `gpu-jmendoza-arenas-*` QoS, and a
`NodeList` on mems-n0. If the account is anything other than `jmendoza-arenas`, you left out
`--account=jmendoza-arenas`. If you see `a100`, `l40s`, or `preempt` instead of `jmendoza-arenas`, you left out `--partition=jmendoza-arenas`.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `User's group not permitted to use this partition` | You are not in the `jmendoza-arenas` group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| `Invalid qos specification` | You submitted under a different account instead of `jmendoza-arenas`. | Add `--account=jmendoza-arenas` (or `-a jmendoza-arenas` for `crc-interactive`) and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the `jmendoza-arenas` Slurm account yet. | Run the `sacctmgr show assoc` check above. If it returns nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w mems-n0` shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, node full of `jmendoza-arenas` jobs | Your group is using the GPUs you need. | Wait, request fewer GPUs, or coordinate within your group. |
| `ERROR: You must request a GPU (--gres=gpu:N) to run on this cluster!` | The job has no `--gres` request. | Add `--gres=gpu:N` (or `-u N` for `crc-interactive`). |
| `Invalid generic resource (gres) specification` | More than 10 GPUs, or a typed request such as `gpu:a40:2`. | Use `--gres=gpu:N` with N from 1 to 10. |
| Job rejected for requesting too many CPUs | More than 56 cores requested. | Request at most 56 cores. |
| `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days. | Request `--time=6-00:00:00` or less, and checkpoint longer runs. |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M gpu -p jmendoza-arenas` and watch for CRCD maintenance announcements. |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`. |

## Related

- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of this node works.
- [GPU Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
  describes the rest of the GPU cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
