---
title: vcooper 
---

# vcooper Investor Partition (HTC Cluster)

The `vcooper` partition gives members of the vcooper group **on-demand, priority access** to a dedicated
128-core, 1.5 TB AMD Turin node on the HTC cluster, purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no vcooper member is using it, the node is lent to the broader Pitt community through the HTC
cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so it
never sits idle. When you need it, you simply submit to `vcooper`: Slurm immediately removes whatever
community `preempt` jobs are in the way and starts your job. You don't need to ask anyone, file a
ticket, or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    You must be associated with the `vcooper` Slurm account on the HTC cluster. Check with:

    ```
    sacctmgr show assoc where cluster=htc account=vcooper user=$USER format=Cluster,Account,User
    ```

    If this returns no rows, ask your PI to request that CRCD add you to the `vcooper` account.

!!! tip "Include `--account=vcooper` in your jobs"
    Every `vcooper` job must charge the `vcooper` Slurm account, because the vcooper QoS levels are attached
    only to that account. For current members, `vcooper` is already the default account on the HTC
    cluster, so jobs work without `--account`. Including it is still recommended, because it keeps
    your scripts working if your HTC default ever changes, for example if you join another lab.
    All the examples below include it.

    Default accounts are set **per cluster**, so check the HTC one specifically. The `Def Acct`
    column shows your HTC default:

    ```
    sacctmgr show user $USER withassoc where cluster=htc format=Cluster,User,DefaultAccount,Account
    ```

## The hardware

| Partition | Nodes | `--constraint` | CPU                   | Cores | Mem/Node | Mem/Core | Max `--mem` per job | Scratch     | Node Name |
| --------- | ----- | -------------- | --------------------- | ----- | -------- | -------- | ------------------- | ----------- | --------- |
| vcooper | 1     | amd,turin      | 2× AMD EPYC 9575F     | 128   | 1.5 TB   | 12 GB    | `1540000M`          | 2.9 TB NVMe | htc-n92   |

The node has two 64-core AMD EPYC 9575F (Turin) processors and 2.9 TB of local NVMe scratch,
mounted at `/scratch`. Staging input data there, instead of reading it repeatedly from shared
storage, speeds up I/O-heavy jobs.

!!! warning "Set `--mem`"
    If you don't specify memory, an HTC job gets the cluster default of **8 GB per core**. That's
    less than the 12 GB per core this node provides, so memory-hungry jobs should request what they
    need with `--mem` (per node) or `--mem-per-cpu`, up to `1540000M` for the whole node.

!!! note "Single-node jobs only"
    `vcooper` is a single node, so every job runs on htc-n92, with at most 128 cores and about 1.5 TB of
    memory. A job that asks for more nodes is rejected at submission with
    `ERROR: Maximum node count is 1!`. For multi-node CPU work, use the MPI cluster.

!!! success "No Service Unit cost"
    The `vcooper` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

The same node, htc-n92, belongs to two partitions at once:

|                     | `vcooper` (your partition) | `preempt` (community)                           |
| ------------------- | -------------------------- | ----------------------------------------------- |
| Who can submit      | vcooper members only             | Anyone with an HTC cluster account              |
| SU cost             | 0                         | 0                                               |
| Can be preempted?   | **No**                    | **Yes**, stopped immediately when vcooper needs it  |
| Scheduling priority | Investor (highest)        | Lowest                                          |

When you submit a job to `vcooper`, this is what happens:

1. Slurm checks whether htc-n92 has enough free cores and memory for your request.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. There is no grace period: those jobs are killed immediately and lose any
   work in progress. Batch jobs are normally put back in the queue, but they start over from the
   beginning; interactive sessions simply end. Either way, the node is freed for you at once.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    vcooper members. If your group is already using the cores or memory you need, your job waits in
    the queue like any normal job until enough of those jobs finish.

    There is **no per-person or per-group cap** on the vcooper QoS levels. A single member can
    occupy the whole node, all 128 cores and 1.5 TB, for up to 21 days. Please coordinate large or
    long jobs within your group, and ask for only the cores, memory, and walltime you actually
    need. When the node is busy, you can also run on the community `htc`
    partition, which charges Service Units to your normal account.

    Like all cluster hardware, htc-n92 is also unavailable during cluster-wide maintenance.

## Step 1: See what is on the node (optional)

You never need to check before submitting, but it helps to know what to expect. To see how busy
the node is:

```
sinfo -M htc -p vcooper -N -O "NodeList:12,StateCompact:10,CPUsState:20,AllocMem:12,Memory:12"
```

`CPUsState` shows allocated, idle, other, and total cores. To see which jobs are on the node and
which partition they came from:

```
squeue -M htc -w htc-n92 -o "%.12i %.10P %.10u %.3t %.12M %.5C %m"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `vcooper` belong to your group and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab cores for testing, debugging, or exploratory
work. See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 8 cores and 64 GB of memory for 4 hours:

```
crc-interactive -d -p vcooper -a vcooper -c 8 -b 64 -t 4
```

| Flag | Meaning                                  |
| ---- | ---------------------------------------- |
| `-d` | use the HTC cluster                      |
| `-p` | partition, which must be `vcooper` |
| `-a` | Slurm account, which should be `vcooper` |
| `-c` | number of cores (default: 1)             |
| `-b` | memory in GB                             |
| `-t` | walltime in hours, or `hours:minutes` (default: 1 hour) |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` defaults to a single core and a small amount of memory. Give `-c` and `-b`
    explicitly. Add `-z` to print the equivalent `srun` command without running it, and `-o` for
    OpenMP-style programs.

### With `srun`

The equivalent direct Slurm command is:

```
srun -M htc --partition=vcooper --account=vcooper --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=8 --mem=64G --time=04:00:00 --pty bash
```

When your prompt changes to `htc-n92`, you are on the vcooper node.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.
    Cores you hold but don't use are unavailable both to your group and to the community.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example runs a 16-core job with 128 GB of
memory for 3 days:

```bash
#!/bin/bash
#SBATCH --job-name=vcooper-job
#SBATCH --cluster=htc
#SBATCH --partition=vcooper
#SBATCH --account=vcooper
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=128G
#SBATCH --time=3-00:00:00
#SBATCH --output=%x-%j.out

module purge
module load <your-modules>

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
./my_program

crc-job-stats
```

Submit it from a login node with:

```
sbatch vcooper-job.slurm
```

To take the entire node, use `--cpus-per-task=128` and `--mem=1540000M`.

!!! tip "Many small jobs"
    HTC workloads are often many independent tasks. A [job array](https://crc-pages.pitt.edu/user-manual/slurm/job-arrays/)
    on `vcooper` works the same as on `htc`. The array index limit is 500, and you can use the `%N`
    suffix, for example `--array=0-499%16`, to cap how much of the node your array occupies at once.

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `vcooper` jobs cannot be preempted by community users, so you don't
    need `--requeue` or preemption-proof checkpointing. Checkpointing long runs is still good
    practice in case of node failure or maintenance.

## Walltime and QoS

`vcooper` jobs follow the same walltime tiers as the rest of the HTC cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the vcooper investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching vcooper QoS automatically:

| Your `--time`               | Assigned QoS      |
| --------------------------- | ---------------- |
| up to 1-00:00:00 (1 day)    | `htc-vcooper-s`             |
| up to 3-00:00:00 (3 days)   | `htc-vcooper-n`             |
| up to 6-00:00:00 (6 days)   | `htc-vcooper-l`             |
| up to 21-00:00:00 (21 days) | `htc-vcooper-ll`            |
| more than 21 days           | rejected                |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the vcooper QoS levels have no CPU, memory, or job-count limits. The only
limit is the node itself. All four tiers can preempt community `preempt` jobs.

To see the current vcooper QoS settings, run:

```
sacctmgr show qos where name=htc-vcooper-s,htc-vcooper-n,htc-vcooper-l,htc-vcooper-ll format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%60
```

## Confirm your job is on `vcooper`

Check your jobs on the HTC cluster:

```
squeue -M htc -u $USER
```

To see every job in the vcooper group, which is useful when the node is busy:

```
squeue -M htc -A vcooper
```

Check one job's details:

```
scontrol -M htc show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=vcooper`, `Partition=vcooper`, an `htc-vcooper-*` QoS, and
`NodeList=htc-n92`. If the account is anything other than `vcooper`, you left out `--account=vcooper`. If
you see `htc` or `preempt` instead of `vcooper`, you left out `--partition=vcooper`. Jobs submitted to
`preempt` can be preempted at any time, and jobs on `htc` are charged Service Units.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `Invalid qos specification` | The job was charged to an account other than `vcooper`, for example because your HTC default changed. | Add `--account=vcooper` and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the `vcooper` Slurm account yet. | Run the `sacctmgr show assoc` check above. If it returns nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w htc-n92` shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, node full of `vcooper` jobs | Your group is using the resources you need. | Wait, request less, coordinate within your group, or run on `htc` (charged SUs). |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | The node is down, drained, or reserved for maintenance. | Check `sinfo -M htc -p vcooper` and watch for CRCD maintenance announcements. |
| `ERROR: Maximum walltime is 21 days!` | `--time` is longer than 21 days, the HTC maximum. | Request `--time=21-00:00:00` or less, and checkpoint longer runs. |
| `ERROR: Maximum node count is 1!` | The job asks for more than one node. | `vcooper` is a single node. Use `--nodes=1`, or run multi-node work on the MPI cluster. |
| `Requested node configuration is not available` | The job asks for more than 128 cores or more memory than the node has. | Request at most 128 cores and `--mem=1540000M`. |
| Job killed with an out-of-memory error | The job used the default of 8 GB per core. | Set `--mem` or `--mem-per-cpu` to what the job needs. |

## Related

- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of this node works.
- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/),
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/), and
  [Job Arrays](https://crc-pages.pitt.edu/user-manual/slurm/job-arrays/) cover job submission in
  general.
- [HTC Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/htc/)
  describes the rest of the HTC cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
