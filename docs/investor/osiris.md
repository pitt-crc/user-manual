---
title: osiris 
---

# osiris Investor Partition (SMP Cluster)

The `osiris` partition gives three research groups, **azentner**, **cbadenes**, and **dhillier**,
shared **on-demand, priority access** to three dedicated CPU nodes on the SMP cluster (324 cores
in total), purchased under the
[Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/).

When no osiris member is using them, the nodes are lent to the broader Pitt community through the
SMP cluster's [`preempt` partition](https://crc-pages.pitt.edu/user-manual/slurm/preempt/), so
they never sit idle. When you need them, you simply submit to `osiris`: Slurm immediately removes
whatever community `preempt` jobs are in the way and starts your job. You don't need to ask
anyone, file a ticket, or wait for other users' jobs to finish.

!!! info "Who can use this partition"
    Members of the `azentner`, `cbadenes`, or `dhillier` Slurm accounts on the SMP cluster. To see
    which of your accounts has osiris access, run:

    ```
    sacctmgr -P show assoc where cluster=smp user=$USER format=Account,QOS | grep smp-osiris | cut -d'|' -f1
    ```

    This prints the account name(s) to use with osiris. If it prints nothing, ask your PI to
    request that CRCD add you to their account.

!!! tip "Include `--account=<group>` in your jobs"
    osiris has no account of its own. Its QoS levels are attached to the three group accounts, so
    every `osiris` job must charge **your group's account**: `azentner`, `cbadenes`, or `dhillier`.
    For most members this is already their default account, so jobs work without `--account`.
    Including it is still recommended, because it keeps your scripts working if your default
    account ever changes, for example if you join another lab. In the examples below, replace
    `<group>` with the account printed by the check above, for example `--account=cbadenes`.

    Default accounts are set **per cluster**, so check the SMP one specifically. The `Def Acct`
    column shows your SMP default:

    ```
    sacctmgr show user $USER withassoc where cluster=smp format=Cluster,User,DefaultAccount,Account
    ```

## The hardware

The partition combines two Intel nodes with one AMD Turin node:

| Node      | `--constraint`     | CPU                        | Cores | Mem/Node | Mem/Core | Max `--mem` per job | Scratch     |
| --------- | ------------------ | -------------------------- | ----- | -------- | -------- | ------------------- | ----------- |
| smp-n265  | amd,turin          | AMD EPYC 9755              | 256   | 1.5 TB   | 6 GB     | `1540000M`          | 3.2 TB NVMe |
| osiris-n1 | intel,cascade_lake | 2× Intel Xeon Gold 6240R   | 48    | 754 GB   | 16 GB    | `772000M`           | 1.8 TB      |
| osiris-n0 | intel,cascade_lake | 1× Intel Xeon Gold 5218R   | 20    | 376 GB   | 19 GB    | `385000M`           | 20 TB       |

Jobs without a `--constraint` go to whichever node has room. Use `--constraint=turin` or
`--constraint=intel` to choose an architecture. Both Intel nodes have the same features, so to
target one of them specifically, use `--nodelist`. For example, `--nodelist=osiris-n1` gets the
larger Intel node, and `--nodelist=osiris-n0` gets the node with the most local scratch (20 TB),
which suits jobs that stage large datasets into `/scratch`.

!!! warning "Set `--mem`"
    If you don't specify memory, an SMP job gets the cluster default of about **4 GB per core**.
    That's much less than these nodes provide per core, and a common cause of out-of-memory
    failures. Request what you need with `--mem` (per node) or `--mem-per-cpu`, up to the per-node
    maximums in the table above.

!!! note "Single-node jobs only"
    `osiris` jobs are limited to **one node**. A job that asks for more nodes is rejected at
    submission with `ERROR: Maximum node count is 1!`. The largest single job is the whole Turin
    node: 256 cores and about 1.5 TB of memory. For multi-node CPU work, use the MPI cluster.

!!! success "No Service Unit cost"
    The `osiris` partition has billing weights of `0`. Jobs here do **not** draw down your group's
    [Service Unit](https://crc-pages.pitt.edu/user-manual/slurm/service-units/) allocation.

## How on-demand access works

All three nodes belong to two partitions at once:

|                     | `osiris` (your partition) | `preempt` (community)                             |
| ------------------- | ------------------------- | ------------------------------------------------- |
| Who can submit      | the three osiris groups   | Anyone with an SMP cluster account                |
| SU cost             | 0                         | 0                                                 |
| Can be preempted?   | **No**                    | **Yes**, stopped immediately when osiris needs it |
| Scheduling priority | Investor (highest)        | Lowest                                            |

When you submit a job to `osiris`, this is what happens:

1. Slurm looks for an osiris node with enough free cores and memory for your request.
2. If community `preempt` jobs are holding the resources you need, Slurm stops just enough of
   them to fit your job. There is no grace period: those jobs are killed immediately and lose any
   work in progress. Batch jobs are normally put back in the queue, but they start over from the
   beginning; interactive sessions simply end. Either way, the node is freed for you at once.
3. Your job starts as soon as the node finishes cleaning up after the preempted jobs, which
   normally takes no more than a minute or two.

!!! warning "What preemption does *not* do"
    Preemption only displaces **community `preempt` jobs**. It never displaces jobs from osiris
    members, in your group or in the other two. If osiris jobs are already using the resources
    you need, your job waits in the queue like any normal job until enough of them finish.

    There is **no per-person or per-group cap** on the osiris QoS levels. A single member of any
    of the three groups can occupy every osiris node, including the whole 256-core Turin node, for
    up to 21 days. Because the nodes are shared across three groups, please coordinate large or
    long jobs with the other groups, and ask for only the cores, memory, and walltime you actually
    need. When the osiris nodes are busy, you can also run on the
    community `smp` partition, which charges Service Units to your normal account.

    Like all cluster hardware, the osiris nodes are also unavailable during cluster-wide
    maintenance.

## Step 1: See what is on the nodes (optional)

You never need to check before submitting, but it helps to know what to expect. To see how busy
each osiris node is:

```
sinfo -M smp -p osiris -N -O "NodeList:12,StateCompact:10,Features:20,CPUsState:20,AllocMem:12,Memory:12"
```

`CPUsState` shows allocated, idle, other, and total cores. To see which jobs are on the nodes and
which partition they came from:

```
squeue -M smp -w osiris-n[0-1],smp-n265 -o "%.12i %.10P %.10u %.3t %.12M %.5C %N"
```

Jobs listed under `preempt` belong to community users and will be preempted to make room for you.
Jobs listed under `osiris` belong to one of the three osiris groups and will not be.

## Step 2a: Start an interactive session

Interactive sessions are the quickest way to grab cores for testing, debugging, or exploratory
work. See [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) for
background.

### With `crc-interactive`

This requests 8 cores and 64 GB of memory for 4 hours:

```
crc-interactive -s -p osiris -a <group> -c 8 -b 64 -t 4
```

| Flag | Meaning                               |
| ---- | ------------------------------------- |
| `-s` | use the SMP cluster                   |
| `-p` | partition, which must be `osiris`     |
| `-a` | your group's Slurm account            |
| `-c` | number of cores                       |
| `-b` | memory in GB                          |
| `-t` | walltime in hours, or `hours:minutes` |

!!! tip "Always pass `-c` and `-b`"
    `crc-interactive` supplies its own small defaults for cores and memory. Give `-c` and `-b`
    explicitly. Add `-z` to print the equivalent `srun` command without running it.

### With `srun`

The equivalent direct Slurm command is:

```
srun -M smp --partition=osiris --account=<group> --nodes=1 --ntasks-per-node=1 \
     --cpus-per-task=8 --mem=64G --time=04:00:00 --pty bash
```

When your prompt changes to `osiris-n0`, `osiris-n1`, or `smp-n265`, you are on an osiris node.

!!! note "Release the node when you are done"
    Type `exit` to end the session. If you started the session with `salloc`, also run
    `scancel <jobid>`, because the allocation stays held until its walltime expires otherwise.
    Cores you hold but don't use are unavailable both to your group and to the community.

## Step 2b: Submit a batch job

For longer or unattended work, use a batch script. This example runs a 16-core multithreaded job
with 256 GB of memory for 3 days:

```bash
#!/bin/bash
#SBATCH --job-name=osiris-job
#SBATCH --cluster=smp
#SBATCH --partition=osiris
#SBATCH --account=<group>
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=16
#SBATCH --mem=256G
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
sbatch osiris-job.slurm
```

To take the entire Turin node, use `--constraint=turin`, `--cpus-per-task=256`, and
`--mem=1540000M`.

!!! info "No checkpointing required"
    Unlike `preempt` jobs, your `osiris` jobs cannot be preempted by community users, so you don't
    need `--requeue` or preemption-proof checkpointing. Checkpointing long runs is still good
    practice in case of node failure or maintenance.

## Walltime and QoS

`osiris` jobs follow the same walltime tiers as the rest of the SMP cluster
(see [Job Limits & QoS](https://crc-pages.pitt.edu/user-manual/slurm/job-limits/)), using QoS
levels dedicated to the osiris investment. **You don't choose the QoS yourself.** When you submit,
the cluster's submit filter reads your `--time` and assigns the matching osiris QoS automatically:

| Your `--time`               | Assigned QoS     |
| --------------------------- | ---------------- |
| up to 1-00:00:00 (1 day)    | `smp-osiris-s`   |
| up to 3-00:00:00 (3 days)   | `smp-osiris-n`   |
| up to 6-00:00:00 (6 days)   | `smp-osiris-l`   |
| up to 21-00:00:00 (21 days) | `smp-osiris-ll`  |
| more than 21 days           | rejected         |

So you don't need `--qos`; just set `--time` to what your job actually needs. Always give
`--time` explicitly. If you leave it out, a cluster default walltime is applied, which may not
match your job.

Apart from walltime, the osiris QoS levels have no CPU, memory, or job-count limits. The only
limit is the hardware itself. All four tiers can preempt community `preempt` jobs.

To see the current osiris QoS settings, run:

```
sacctmgr show qos where name=smp-osiris-s,smp-osiris-n,smp-osiris-l,smp-osiris-ll format=Name,MaxWall,MaxTRESPU%30,MaxTRESPA%30,Preempt%60
```

## Confirm your job is on `osiris`

Check your jobs on the SMP cluster:

```
squeue -M smp -u $USER
```

To see every osiris job from all three groups, which is useful when the nodes are busy:

```
squeue -M smp -p osiris
```

Check one job's details:

```
scontrol -M smp show job <jobid> | grep -E "Account|Partition|QOS|NodeList|TRES"
```

A correctly submitted job shows `Account=` one of `azentner`, `cbadenes`, or `dhillier`,
`Partition=osiris`, an `smp-osiris-*` QoS, and a `NodeList` of `osiris-n0`, `osiris-n1`, or
`smp-n265`. If the account is anything else, you left out `--account=<group>`. If you see `smp`,
`high-mem`, or `preempt` instead of `osiris`, you left out `--partition=osiris`. Jobs submitted
to `preempt` can be preempted at any time, and jobs on `smp` or `high-mem` are charged Service
Units.

## Troubleshooting

| What you see | Likely cause | What to do |
| --- | --- | --- |
| `Invalid qos specification` | You submitted under an account that doesn't have osiris access. | Add `--account=<group>` (or `-a <group>` for `crc-interactive`) and resubmit. |
| `Invalid account or account/partition combination specified` | You aren't associated with the account you specified. | Run the `sacctmgr show assoc` check above and use the account it prints. If it prints nothing, contact CRCD with your username. |
| Job `PD` with reason `Resources` while `squeue -w` shows `preempt` jobs on the osiris nodes | Preemption is in progress. | Wait a couple of minutes. If it is still pending after about 10 minutes, contact CRCD. |
| Job `PD` with reason `Resources` or `Priority`, nodes full of `osiris` jobs | The osiris groups are using the resources you need. | Wait, request less, coordinate with the other groups, or run on `smp` (charged SUs). |
| Job `PD` with reason `ReqNodeNotAvail` or a maintenance message | A node is down, drained, or reserved for maintenance. | Check `sinfo -M smp -p osiris` and watch for CRCD maintenance announcements. |
| `ERROR: Maximum walltime is 21 days!` | `--time` is longer than 21 days, the SMP maximum. | Request `--time=21-00:00:00` or less, and checkpoint longer runs. |
| `ERROR: Maximum node count is 1!` | The job asks for more than one node. | `osiris` is single-node only. Use `--nodes=1`, or run multi-node work on the MPI cluster. |
| `Requested node configuration is not available` | No osiris node can satisfy the request, for example `--constraint=intel` with more than 48 cores, or more memory than the largest node has. | Compare your request with the hardware table and adjust cores, memory, or `--constraint`. |
| Job killed with an out-of-memory error | The job used the default of about 4 GB per core. | Set `--mem` or `--mem-per-cpu` to what the job needs. |
| Interactive session runs out of memory | `crc-interactive` used its small default memory. | Pass `-b <GB>`. |

## Related

- [Preemptible Partitions](https://crc-pages.pitt.edu/user-manual/slurm/preempt/) explains how
  the community side of these nodes works.
- [Interactive Jobs](https://crc-pages.pitt.edu/user-manual/slurm/interactive-jobs/) and
  [Batch Jobs](https://crc-pages.pitt.edu/user-manual/slurm/batch-jobs/) cover job submission in
  general.
- [SMP Cluster Hardware Profile](https://crc-pages.pitt.edu/user-manual/hardware_profiles/smp/)
  describes the rest of the SMP cluster.
- [Hardware Investing Policy](https://crc-pages.pitt.edu/user-manual/policies/hardware-investing-policy/)
  explains how investor partitions are set up.
