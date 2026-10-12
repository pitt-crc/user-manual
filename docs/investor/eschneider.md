---
title: eschneider 
---

# eschneider Investor Partition (GPU Cluster)

The `eschneider` partition gives members of the eschneider group **dedicated, priority access** to five GPU
nodes on the GPU cluster, each with 4× NVIDIA V100 32 GB GPUs (20 GPUs in total). The nodes were purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

!!! info "Who can use this partition"
    Members of the `eschneider` Unix group. To check your membership, run:

    ```
    id -Gn | tr ' ' '\n' | grep -x eschneider
    ```

    If this prints `eschneider`, you have access. If it prints nothing, ask your PI to request that
    CRCD add you to the group.

!!! tip "Charge your jobs to `--account=eschneider`"
    Access to `eschneider` is controlled by the Unix group, and jobs here cost no Service Units, so a job
    runs under whichever of your GPU accounts it is charged to. Please still add
    `--account=eschneider` (or `-A eschneider`) to your jobs, as all the examples below do, so the usage is
    recorded against the eschneider investment. For most members `eschneider` is already the default GPU
    account. To check yours, look at the `Def Acct` column of:

    ```
    sacctmgr show user $USER withassoc where cluster=gpu format=Cluster,User,DefaultAccount,Account
    ```

## The hardware

| Partition | Nodes | GPU | VRAM | GPU/Node | CPU | Physical cores/Node | Slurm CPUs/Node | Mem/Node | Max `--mem` per job | Scratch | Node Names |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| eschneider | 5 | NVIDIA V100 SXM2 | 32 GB | 4 | 2× IBM POWER9 (`ppc64le`) | 32 | 128 | ~511 GB | `523300M` | 503 GB | ppc-n[0-4] |

!!! danger "These are POWER nodes, not x86"
    The `ppc-n` nodes use IBM POWER9 processors (`ppc64le` architecture). Software built for the x86
    login nodes and other clusters **won't run here**. Use modules and containers built for
    `ppc64le`, and compile your own code on a `eschneider` node, for example in an interactive session.
    Python environments created elsewhere generally need to be rebuilt on these nodes.

!!! note "Slurm counts hardware threads as CPUs"
    Each physical core runs 4 hardware threads, and Slurm counts each thread as a CPU: 32 cores
    appear as 128 CPUs. Slurm allocates whole cores, so CPU requests are effectively rounded up to
    a multiple of 4. For example, `--cpus-per-task=8` gives you 2 physical cores.

Every GPU you request comes with a default share of the node's CPUs and memory:

| GPUs per node (`--gres=gpu:N`) | Default CPUs (physical cores) | Default memory |
| --- | --- | --- |
| 1 | 32 (8) | ~128 GB |
| 2 | 64 (16) | ~255 GB |
| 4 (whole node) | 128 (32) | ~511 GB |

These defaults come from the partition settings of 32 CPUs per GPU and 4,085 MB of memory per CPU.
You can override them with `--cpus-per-task` and `--mem`, up to 128 CPUs and `523300M` per node.
There is no limit on CPUs per GPU.

!!! note "GPUs are optional here"
    `eschneider` accepts jobs that don't request a GPU, so you can run CPU-only work on these nodes. To
    use GPUs, request them with `--gres=gpu:N` (N = 1 to 4 per node), in the plain `gpu:N` form.

!!! success "No Service Unit cost"
    The `eschneider` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## A dedicated partition

All five nodes, ppc-n[0-4], are reserved for the eschneider group. They are **not** part of the GPU
cluster's community `preempt` partition, so:

- **Your jobs are never preempted.** No other user's job can stop them.
- **Only your group's jobs run here.** When the group isn't using the nodes, they sit idle.
- **Waiting means your group is busy.** If your job is pending, it is waiting behind other
  eschneider jobs, or for maintenance to finish.

Please coordinate large or long jobs within your group, and ask for only the GPUs, cores, and
walltime you actually need.

## See what is on the nodes (optional)

To see how busy each eschneider node is:

```
sinfo -M gpu -p eschneider -N -O "NodeList:12,StateCompact:10,Gres:14,GresUsed:24,CPUsState:16,AllocMem:10"
```

To see which eschneider jobs are running or waiting:

```
squeue -M gpu -p eschneider
```

## Start an interactive session

Interactive sessions are the quickest way to grab GPUs for testing, debugging, or exploratory work.
See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 1 GPU, 32 cores, and 128 GB of memory for 4 hours:

```
crc-interactive -g -p eschneider -a eschneider -u 1 -c 32 -b 128 -t 4
```

| Flag | Meaning |
| --- | --- |
| `-g` | use the GPU cluster |
| `-p` | partition, which must be `eschneider` |
| `-a` | Slurm account, which should be `eschneider` |
| `-u` | number of GPUs |
| `-c` | number of CPUs (default: 1) |
| `-b` | memory in GB |
| `-t` | walltime in hours, or `hours:minutes` (default: 1 hour) |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` defaults to a single CPU and a small amount of memory, which can override
    the partition's per-GPU defaults. Give `-c` and `-b` explicitly. Add `-z` to print the
    equivalent `srun` command without running it.

The GPU request is optional on `eschneider`. For a CPU-only session, leave out `-u` (or `--gres` with
`srun`).

### With `srun`

The equivalent direct Slurm command is:

```
srun -M gpu --partition=eschneider --account=eschneider --gres=gpu:1 --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=32 --mem=128G --time=04:00:00 --pty bash
```

When your prompt changes to one of `ppc-n0` to `ppc-n4`, you are on a eschneider node. Confirm your GPUs with `nvidia-smi`.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.

## Submit a batch job

For longer or unattended work, use a batch script. This example requests a whole node (4 GPUs) for
1 day:

```bash
#!/bin/bash
#SBATCH --job-name=eschneider-job
#SBATCH --cluster=gpu
#SBATCH --partition=eschneider
#SBATCH --account=eschneider
#SBATCH --nodes=1
#SBATCH --gres=gpu:4
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=128      # optional: matches the per-GPU default
#SBATCH --mem=500G               # optional: matches the per-GPU default
#SBATCH --time=1-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>      # use ppc64le builds

nvidia-smi
python train.py

crc-job-stats
```

Submit it from a login node with:

```
sbatch eschneider-job.slurm
```

### Multi-node jobs

A job can span up to all **five** nodes (20 GPUs). `--gres=gpu:N` is **per node**:

```
#SBATCH --nodes=2
#SBATCH --gres=gpu:4            # 4 GPUs on EACH node = 8 GPUs total
#SBATCH --ntasks-per-node=4     # one task per GPU
#SBATCH --cpus-per-task=32
```

## Walltime and QoS

`eschneider` jobs follow the same walltime tiers as the rest of the GPU cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the eschneider investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching QoS automatically:

| Your `--time` | Assigned QoS |
| --- | --- |
| up to 1-00:00:00 (1 day) | `gpu-eschneider-s` |
| up to 3-00:00:00 (3 days) | `gpu-eschneider-n` |
| up to 6-00:00:00 (6 days) | `gpu-eschneider-l` |
| more than 6 days | rejected |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Shorter tiers have higher scheduling priority, so a 1-day job usually starts ahead of a 6-day job
when both are waiting. Each person can have up to **100 jobs running** at once, and each account up
to **500**. There are no GPU, CPU, or memory caps beyond the hardware itself.

To see the current eschneider QoS settings, run:

```
sacctmgr show qos where name=gpu-eschneider-s,gpu-eschneider-n,gpu-eschneider-l format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%50
```

## Confirm your job is on `eschneider`

Check your jobs on the GPU cluster, and every job in the eschneider group:

```
squeue -M gpu -u $USER
squeue -M gpu -A eschneider
```

Check one job's details:

```
scontrol -M gpu show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Partition=eschneider`, a `gpu-eschneider-*` QoS, ideally `Account=eschneider`, and a
`NodeList` on ppc-n[0-4]. If the account isn't `eschneider`, add `--account=eschneider` next
time so the usage is attributed to the investment. If you see `a100`, `l40s`, or `preempt` instead of `eschneider`, you left out `--partition=eschneider`.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `User's group not permitted to use this partition` | You are not in the `eschneider` group, or you were added very recently. | Check with `id -Gn`. If you were just added, log out, log back in, and allow a few minutes for Slurm to refresh its group membership. |
| `Invalid account or account/partition combination specified` | You specified an account you don't belong to. | Use `--account=eschneider` if you are in that account, or one of your own GPU accounts. Contact CRCD if you think you should be in the `eschneider` account. |
| `Illegal instruction`, `cannot execute binary file`, or `Exec format error` | You ran software built for x86. | Use a `ppc64le` build: load modules on the node itself, rebuild your environment there, or use a `ppc64le` container. |
| Job `PD` with reason `Resources` or `Priority` | Other eschneider jobs are using the GPUs or CPUs you need. | Wait, request less, or coordinate within your group. |
| Job rejected for requesting too many CPUs | More than 128 CPUs requested on a node. | Request at most 128 CPUs (32 physical cores) per node. |
| `Invalid generic resource (gres) specification` | More than 4 GPUs per node, or a typed request such as `gpu:v100:2`. | Use `--gres=gpu:N` with N from 1 to 4. |
| `ERROR: Maximum walltime is 6 days.` | `--time` is longer than 6 days. | Request `--time=6-00:00:00` or less, and checkpoint longer runs. |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M gpu -p eschneider` and watch for CRCD maintenance announcements. |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`. |

## Related

- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [GPU Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/gpu/)
  describes the rest of the GPU cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
