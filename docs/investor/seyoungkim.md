---
title: seyoungkim 
---

# seyoungkim Investor Partition (HTC Cluster)

The `seyoungkim` partition gives members of the seyoungkim group **on-demand, priority access** to a dedicated
128-core, 1.5 TB AMD Turin node on the HTC cluster, purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no seyoungkim member is using it, the node is lent to the broader Pitt community through the HTC
cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so it
never sits idle. When you need it, you simply submit to `seyoungkim`: Slurm immediately removes whatever
community `preempt` jobs are in the way and starts your job. You don't need to ask anyone, file a
ticket, or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    You must be associated with the `seyoungkim` Slurm account on the HTC cluster. Check with:

    ```
    sacctmgr show assoc where cluster=htc account=seyoungkim user=$USER format=Cluster,Account,User
    ```

    If this returns no rows, ask your PI to request that CRCD add you to the `seyoungkim` account.

!!! tip "Include `--account=seyoungkim` in your jobs"
    Every `seyoungkim` job must charge the `seyoungkim` Slurm account, because the seyoungkim QoS levels are attached
    only to that account. For current members, `seyoungkim` is already the default account on the HTC
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
| seyoungkim | 1     | amd,turin      | 2× AMD EPYC 9575F     | 128   | 1.5 TB   | 12 GB    | `1540000M`          | 2.9 TB NVMe | htc-n93   |

The node has two 64-core AMD EPYC 9575F (Turin) processors and 2.9 TB of local NVMe scratch,
mounted at `/scratch`. Staging input data there, instead of reading it repeatedly from shared
storage, speeds up I/O-heavy jobs.

!!! warning "Set `--mem`"
    If you don't specify memory, an HTC job gets the cluster default of **8 GB per core**. That's
    less than the 12 GB per core this node provides, so memory-hungry jobs should request what they
    need with `--mem` (per node) or `--mem-per-cpu`, up to `1540000M` for the whole node.

!!! note "Single-node jobs only"
    `seyoungkim` is a single node, so every job runs on htc-n93, with at most 128 cores and about 1.5 TB of
    memory. A job that asks for more nodes is rejected at submission with
    `ERROR: Maximum node count is 1!`. For multi-node CPU work, use the MPI cluster.

!!! success "No Service Unit cost"
    The `seyoungkim` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

The same node, htc-n93, belongs to two partitions at once:

|                     | `seyoungkim` (your partition) | `preempt` (community)                           |
| ------------------- | ----------------------------- | ----------------------------------------------- |
| Who can submit      | seyoungkim members only             | Anyone with an HTC cluster account              |
| SU cost             | 0                            | 0                                               |
| Can be preempted?   | **No**                       | **Yes**, stopped immediately when seyoungkim needs it  |
| Scheduling priority | Investor (highest)           | Lowest                                          |

When you submit a job to `seyoungkim`, this is what happens:

1. Slurm checks whether htc-n93 has enough free cores and memory for your request.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. There is no grace period: those jobs are killed immediately and lose any
   work in progress. Batch jobs are normally put back in the queue, but they start over from the
   beginning; interactive sessions simply end. Either way, the node is freed for you at once.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from other
    seyoungkim members. If your group is already using the cores or memory you need, your job waits in
    the queue like any normal job until enough of those jobs finish.

    There is **no per-person or per-group cap** on the seyoungkim QoS levels. A single member can
    occupy the whole node, all 128 cores and 1.5 TB, for up to 21 days. Please coordinate large or
    long jobs within your group, and ask for only the cores, memory, and walltime you actually
    need. When the node is busy, you can also run on the community `htc`
    partition, which charges Service Units to your normal account.

    Like all cluster hardware, htc-n93 is also unavailable during cluster-wide maintenance.

## Step 1: See what is on the node (optional)

You never need to check before submitting, but it helps to know what to expect. To see how busy
the node is:

```
sinfo -M htc -p seyoungkim -N -O "NodeList:12,StateCompact:10,CPUsState:20,AllocMem:12,Memory:12"
```

`CPUsState` shows allocated, idle, other, and total cores. To see which jobs are on the node and
which partition they came from:

```
squeue -M htc -w htc-n93 -o "%.12i %.10P %.10u %.3t %.12M %.5C %m"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `seyoungkim` belong to your group and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab cores for testing, debugging, or exploratory
work. See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 8 cores and 64 GB of memory for 4 hours:

```
crc-interactive -d -p seyoungkim -a seyoungkim -c 8 -b 64 -t 4
```

| Flag | Meaning                                  |
| ---- | ---------------------------------------- |
| `-d` | use the HTC cluster                      |
| `-p` | partition, which must be `seyoungkim` |
| `-a` | Slurm account, which should be `seyoungkim` |
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
srun -M htc --partition=seyoungkim --account=seyoungkim --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=8 --mem=64G --time=04:00:00 --pty bash
```

When your prompt changes to `htc-n93`, you are on the seyoungkim node.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.
    Cores you hold but don't use are unavailable both to your group and to the community.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example runs a 16-core job with 128 GB of
memory for 3 days:

```bash
#!/bin/bash
#SBATCH --job-name=seyoungkim-job
#SBATCH --cluster=htc
#SBATCH --partition=seyoungkim
#SBATCH --account=seyoungkim
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
sbatch seyoungkim-job.slurm
```

To take the entire node, use `--cpus-per-task=128` and `--mem=1540000M`.

!!! tip "Many small jobs"
    HTC workloads are often many independent tasks. A [job array](https://crc-pages.pitt.edu/user-manual/slurm/job-arrays/)
    on `seyoungkim` works the same as on `htc`. The array index limit is 500, and you can use the `%N`
    suffix, for example `--array=0-499%16`, to cap how much of the node your array occupies at once.

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `seyoungkim` jobs cannot be preempted by community users, so you don't
    need `--requeue` or preemption-proof checkpointing. Checkpointing long runs is still good
    practice in case of node failure or maintenance.

## Walltime and QoS

`seyoungkim` jobs follow the same walltime tiers as the rest of the HTC cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the seyoungkim investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching seyoungkim QoS automatically:

| Your `--time`               | Assigned QoS      |
| --------------------------- | ---------------- |
| up to 1-00:00:00 (1 day)    | `htc-seyoungkim-s`             |
| up to 3-00:00:00 (3 days)   | `htc-seyoungkim-n`             |
| up to 6-00:00:00 (6 days)   | `htc-seyoungkim-l`             |
| up to 21-00:00:00 (21 days) | `htc-seyoungkim-ll`            |
| more than 21 days           | rejected                |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the seyoungkim QoS levels have no CPU, memory, or job-count limits. The only
limit is the node itself. All four tiers can preempt community `preempt` jobs.

To see the current seyoungkim QoS settings, run:

```
sacctmgr show qos where name=htc-seyoungkim-s,htc-seyoungkim-n,htc-seyoungkim-l,htc-seyoungkim-ll format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%60
```

## Confirm your job is on `seyoungkim`

Check your jobs on the HTC cluster:

```
squeue -M htc -u $USER
```

To see every job in the seyoungkim group, which is useful when the node is busy:

```
squeue -M htc -A seyoungkim
```

Check one job's details:

```
scontrol -M htc show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=seyoungkim`, `Partition=seyoungkim`, an `htc-seyoungkim-*` QoS, and
`NodeList=htc-n93`. If the account is anything other than `seyoungkim`, you left out `--account=seyoungkim`. If
you see `htc` or `preempt` instead of `seyoungkim`, you left out `--partition=seyoungkim`. Jobs submitted to
`preempt` can be preempted at any time, and jobs on `htc` are charged Service Units.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `Invalid qos specification` | The job was charged to an account other than `seyoungkim`, for example because your HTC default changed. | Add `--account=seyoungkim` and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the `seyoungkim` Slurm account yet. | Run the `sacctmgr show assoc` check above. If it returns nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w htc-n93` shows `preempt` jobs | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, node full of `seyoungkim` jobs | Your group is using the resources you need. | Wait, request less, coordinate within your group, or run on `htc` (charged SUs). |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | The node is down, drained, or reserved for maintenance. | Check `sinfo -M htc -p seyoungkim` and watch for CRCD maintenance announcements. |
| `ERROR: Maximum walltime is 21 days!` | `--time` is longer than 21 days, the HTC maximum. | Request `--time=21-00:00:00` or less, and checkpoint longer runs. |
| `ERROR: Maximum node count is 1!` | The job asks for more than one node. | `seyoungkim` is a single node. Use `--nodes=1`, or run multi-node work on the MPI cluster. |
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
