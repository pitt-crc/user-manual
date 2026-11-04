---
title: isenocak 
---

# isenocak Investor Partition (GPU Cluster)

The `isenocak` partition gives members of the isenocak group **dedicated, priority access** to two GPU
nodes on the GPU cluster, each with 4× NVIDIA V100 GPUs (8 GPUs in total). The nodes were
purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

!!! info "Who can use this partition"
    Members of the `isenocak` Unix group. To check your membership, run:

    ```
    id -Gn | tr ' ' '\n' | grep -x isenocak
    ```

    If this prints `isenocak`, you have access. If it prints nothing, ask your PI to request that
    CRCD add you to the group.

!!! tip "Charge your jobs to `--account=isenocak`"
    Access to `isenocak` is controlled by the Unix group, and jobs here cost no Service Units, so a job
    runs under whichever of your GPU accounts it is charged to. Please still add
    `--account=isenocak` (or `-A isenocak`) to your jobs, as all the examples below do, so the usage is
    recorded against the isenocak investment. For most members `isenocak` is already the default GPU
    account. To check yours, look at the `Def Acct` column of:

    ```
    sacctmgr show user $USER withassoc where cluster=gpu format=Cluster,User,DefaultAccount,Account
    ```

## The hardware

| Partition | Nodes | GPU | GPU/Node | `--constraint` | CPU | Cores/Node | Mem/Node | Max `--mem` per job | Scratch | Node Names |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| isenocak | 2 | NVIDIA V100 | 4 | v100 | 2× Intel Xeon Gold 6126 | 24 | ~188 GB | `192000M` | 5.5 TB | gpu-n[26-27] |

Every GPU you request comes with a default share of the node's CPUs and memory:

| GPUs per node (`--gres=gpu:N`) | Default cores | Default memory |
| --- | --- | --- |
| 1 | 6 | 48 GB |
| 2 | 12 | 96 GB |
| 4 (whole node) | 24 | 192 GB |

These defaults come from the partition settings of 6 cores per GPU and 8 GB of memory per core.
You can override them with `--cpus-per-task` and `--mem`, up to the node's 24 cores and
`192000M`. There is no limit on cores per GPU.

!!! note "GPUs are optional here"
    Unlike most GPU partitions, `isenocak` accepts jobs that don't request a GPU, so you can run
    CPU-only work such as pre- and post-processing on these nodes. To use GPUs, request them with
    `--gres=gpu:N` (N = 1 to 4 per node), in the plain `gpu:N` form.

!!! success "No Service Unit cost"
    The `isenocak` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## A dedicated partition

Both nodes, gpu-n[26-27], are reserved for the isenocak group. They are **not** part of the GPU
cluster's community `preempt` partition, so:

- **Your jobs are never preempted.** No other user's job can stop them.
- **Only your group's jobs run here.** When the group isn't using the nodes, they sit idle.
- **Waiting means your group is busy.** If your job is pending, it is waiting behind other
  isenocak jobs, or for maintenance to finish.

Please coordinate large or long jobs within your group, and ask for only the GPUs, cores, and
walltime you actually need.

## See what is on the nodes (optional)

To see how busy each isenocak node is:

```
sinfo -M gpu -p isenocak -N -O "NodeList:12,StateCompact:10,Gres:14,GresUsed:24,CPUsState:16,AllocMem:10"
```

To see which isenocak jobs are running or waiting:

```
squeue -M gpu -p isenocak
```

## Start an interactive session

Interactive sessions are the quickest way to grab GPUs for testing, debugging, or exploratory work.
See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 1 GPU, 6 cores, and 48 GB of memory for 4 hours:

```
crc-interactive -g -p isenocak -a isenocak -u 1 -c 6 -b 48 -t 4
```

| Flag | Meaning |
| --- | --- |
| `-g` | use the GPU cluster |
| `-p` | partition, which must be `isenocak` |
| `-a` | Slurm account, which should be `isenocak` |
| `-u` | number of GPUs |
| `-c` | number of cores (default: 1) |
| `-b` | memory in GB |
| `-t` | walltime in hours, or `hours:minutes` (default: 1 hour) |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` defaults to a single core and a small amount of memory, which can override
    the partition's per-GPU defaults. Give `-c` and `-b` explicitly. Add `-z` to print the
    equivalent `srun` command without running it.

The GPU request is optional on `isenocak`. For a CPU-only session, leave out `-u` (or `--gres` with
`srun`).

### With `srun`

The equivalent direct Slurm command is:

```
srun -M gpu --partition=isenocak --account=isenocak --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=6 --mem=48G --time=04:00:00 --pty bash
```

When your prompt changes to `gpu-n26` or `gpu-n27`, you are on a isenocak node. Confirm your GPUs with `nvidia-smi`.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.

## Submit a batch job

For longer or unattended work, use a batch script. This example requests a whole node (4 GPUs) for
1 day:

```bash
#!/bin/bash
#SBATCH --job-name=isenocak-job
#SBATCH --cluster=gpu
#SBATCH --partition=isenocak
#SBATCH --account=isenocak
#SBATCH --nodes=1
#SBATCH --gres=gpu:4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=24      # optional: matches the per-GPU default
#SBATCH --mem=192G               # optional: matches the per-GPU default
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
sbatch isenocak-job.slurm
```

### Two-node jobs

A job can span **both** nodes (8 GPUs). `--gres=gpu:N` is **per node**:

```
#SBATCH --nodes=2
#SBATCH --gres=gpu:4            # 4 GPUs on EACH node = 8 GPUs total
#SBATCH --ntasks-per-node=4     # one task per GPU
#SBATCH --cpus-per-task=6
```

A request for more than 2 nodes is rejected with `ERROR: Maximum node count on isenocak is 2.`

## Walltime and QoS

`isenocak` jobs follow the same walltime tiers as the rest of the GPU cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the isenocak investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching QoS automatically:

| Your `--time` | Assigned QoS |
| --- | --- |
| up to 1-00:00:00 (1 day) | `gpu-isenocak-s` |
| up to 3-00:00:00 (3 days) | `gpu-isenocak-n` |
| up to 6-00:00:00 (6 days) | `gpu-isenocak-l` |
| more than 6 days | rejected |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Shorter tiers have higher scheduling priority, so a 1-day job usually starts ahead of a 6-day job
when both are waiting. Each person can have up to **100 jobs running** at once, and each account up
to **500**. There are no GPU, CPU, or memory caps beyond the hardware itself.

To see the current isenocak QoS settings, run:

```
sacctmgr show qos where name=gpu-isenocak-s,gpu-isenocak-n,gpu-isenocak-l format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `isenocak`

Check your jobs on the GPU cluster, and every job in the isenocak group:

```
squeue -M gpu -u $USER
squeue -M gpu -A isenocak
```

Check one job's details:

```
scontrol -M gpu show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Partition=isenocak`, a `gpu-isenocak-*` QoS, ideally `Account=isenocak`, and a
`NodeList` on gpu-n26 or gpu-n27. If the account isn't `isenocak`, add `--account=isenocak` next
time so the usage is attributed to the investment. If you see `a100`, `l40s`, or `preempt` instead of `isenocak`, you left out `--partition=isenocak`.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `User's group not permitted to use this partition` | You are not in the `isenocak` group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| `Invalid account or account/partition combination specified` | You specified an account you don't belong to. | Use `--account=isenocak` if you are in that account, or one of your own GPU accounts. Contact CRCD if you think you should be in the `isenocak` account. |
| `ERROR: Maximum node count on isenocak is 2.` | The job asks for more than 2 nodes. | Request `--nodes=1` or `--nodes=2`. |
| Job `PD` with reason `Resources` or `Priority` | Other isenocak jobs are using the GPUs or cores you need. | Wait, request less, or coordinate within your group. |
| Job rejected for requesting too many CPUs | More than 24 cores requested on a node. | Request at most 24 cores per node. |
| `Invalid generic resource (gres) specification` | More than 4 GPUs per node, or a typed request such as `gpu:v100:2`. | Use `--gres=gpu:N` with N from 1 to 4. |
| `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days. | Request `--time=6-00:00:00` or less, and checkpoint longer runs. |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M gpu -p isenocak` and watch for CRCD maintenance announcements. |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`. |

## Related

- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [GPU Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
  describes the rest of the GPU cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
