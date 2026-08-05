# Slurm Overview

The CRCD clusters are a shared resource, so access to them is arbitrated by a
scheduler called [**Slurm**](https://slurm.schedmd.com/). You describe the
resources your work needs — a cluster, cores, memory, time, and optionally GPUs
— and Slurm decides when and where your job runs, balancing everyone's requests
fairly.

This section covers how to work with Slurm:

- [**Interactive Jobs**](interactive-jobs.md) — get a compute node and type
  commands live (best for testing and exploring).
- [**Batch Jobs**](batch-jobs.md) — submit a script that runs unattended (best
  for production work).
- [**Service Units**](service-units.md) — how usage is measured and charged.

This page introduces the handful of commands you'll use to see the clusters and
track your jobs.

## Core commands

| Command | What it does |
| ------- | ------------ |
| `sinfo` | Show partition and node status for a cluster |
| `squeue` | List jobs currently queued or running |
| `sbatch` | Submit a batch job script |
| `srun` / `crc-interactive` | Start an interactive session |
| `scontrol show job` | Show full detail for one job |
| `scancel` | Cancel a job |
| `sacct` | Look up a *finished* job's accounting record |

See [**Basic Slurm Commands**](../applications/basic-commands.md) for more, and
[**CRC Wrappers**](../applications/crc-wrappers.md) for the friendlier `crc-*`
helpers.

!!! note "The `-M` (cluster) flag"
    `-M` tells `sinfo`, `squeue`, `scontrol`, `sbatch`, and `scancel` which
    cluster(s) to act on. The value can be any of `smp`, `htc`, `mpi`, `gpu` (comma-separated),
    or `all`. Without it, commands use the primary cluster of the login node
    you're on: **SMP** on `h2p.crc.pitt.edu`, **HTC** on `htc.crc.pitt.edu`.

## Viewing cluster status with `sinfo`

`sinfo` reports the partitions on each cluster and the state of their nodes.

```
[gnowmik@login1 ~]$ sinfo -M mpi,smp,htc,gpu
CLUSTER: gpu
PARTITION         AVAIL  TIMELIMIT  NODES  STATE NODELIST
a100*                up   infinite      5   mix- gpu-n[46,48-49,51,53]
a100*                up   infinite      8    mix gpu-n[35-40,44,47]
a100*                up   infinite      4  alloc gpu-n[45,50,52,54]
a100*                up   infinite      5   idle gpu-n[33-34,41-43]
a100_multi           up   infinite      5   mix- gpu-n[46,48-49,51,53]
a100_multi           up   infinite      1    mix gpu-n47
a100_multi           up   infinite      4  alloc gpu-n[45,50,52,54]
preempt              up   infinite     16   mix- gpu-n[46,48-49,51,53,55-58,60,64-65,67-69],mems-n0
preempt              up   infinite      2  drain gpu-n[72,75]
preempt              up   infinite     11    mix gpu-n[47,73-74,76-77,79-80,82,88-90]
preempt              up   infinite     14  alloc gpu-n[45,50,52,54,59,61-63,66,70-71,83-85]
preempt              up   infinite      4   idle gpu-n[78,81,86-87]
a100_nvlink          up   infinite      2   mix- gpu-n[28-29]
a100_nvlink          up   infinite      3    mix gpu-n[30-32]
a100_nvlink_multi    up   infinite      2   mix- gpu-n[28-29]
a100_nvlink_multi    up   infinite      3    mix gpu-n[30-32]
l40s                 up   infinite     10   mix- gpu-n[55-58,60,64-65,67-69]
l40s                 up   infinite      1  drain gpu-n72
l40s                 up   infinite      1    mix gpu-n73
l40s                 up   infinite      7  alloc gpu-n[59,61-63,66,70-71]
rtx6k                up   infinite      1  drain gpu-n75
rtx6k                up   infinite      6    mix gpu-n[74,76-77,79-80,82]
rtx6k                up   infinite      2   idle gpu-n[78,81]
h200                 up   infinite      2    mix gpu-n[89-90]

CLUSTER: htc
PARTITION  AVAIL  TIMELIMIT  NODES  STATE NODELIST
htc*          up   infinite      1 drain* htc-n26
htc*          up   infinite      2  down* htc-n[71,76]
htc*          up   infinite      1   comp htc-n81
htc*          up   infinite      1   drng htc-n24
htc*          up   infinite     43    mix htc-1024-n[2-3],htc-n[25,27-30,32-33,35-45,47-48,50,52-53,66-70,72,74,77-78,80,82-88,91]
htc*          up   infinite      9  alloc htc-1024-n[0-1],htc-n[31,34,46,73,79,89-90]
htc*          up   infinite     13   idle htc-n[49,51,54-64]
htc*          up   infinite      2   down htc-n[65,75]
preempt       up   infinite      1 drain* htc-n26
preempt       up   infinite      2  down* htc-n[71,76]
preempt       up   infinite      1   comp htc-n81
preempt       up   infinite      1   drng htc-n24
preempt       up   infinite     43    mix htc-1024-n[2-3],htc-n[25,27-30,32-33,35-45,47-48,50,52-53,66-70,72,74,77-78,80,82-88,91]
preempt       up   infinite      9  alloc htc-1024-n[0-1],htc-n[31,34,46,73,79,89-90]
preempt       up   infinite     15   idle htc-n[49,51,54-64,92-93]
preempt       up   infinite      2   down htc-n[65,75]
vcooper       up   infinite      1   idle htc-n92
seyoungkim    up   infinite      1   idle htc-n93

CLUSTER: mpi
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
mpi*           up   infinite      1   plnd mpi-n86
mpi*           up   infinite      3 drain* mpi-n[25,106-107]
mpi*           up   infinite      3 alloc* mpi-n[15,37,53]
mpi*           up   infinite      6  down* mpi-n[6,17,117,119,121,130]
mpi*           up   infinite      6  drain mpi-n[20-23,71,87]
mpi*           up   infinite    115  alloc mpi-n[0-2,4-5,7-14,16,18-19,24,26-36,38-52,54-60,62-70,72-85,88-105,108-116,118,120,122-129,131-135]
mpi*           up   infinite      2   down mpi-n[3,61]
ndr            up   infinite      1 alloc* mpi-n145
ndr            up   infinite      4  down* mpi-n[137-138,142,153]
ndr            up   infinite     13  alloc mpi-n[136,139-141,143-144,146-152]
preempt        up   infinite      1   plnd mpi-n86
preempt        up   infinite      3 drain* mpi-n[25,106-107]
preempt        up   infinite      3 alloc* mpi-n[15,37,53]
preempt        up   infinite      6  down* mpi-n[6,17,117,119,121,130]
preempt        up   infinite      6  drain mpi-n[20-23,71,87]
preempt        up   infinite    115  alloc mpi-n[0-2,4-5,7-14,16,18-19,24,26-36,38-52,54-60,62-70,72-85,88-105,108-116,118,120,122-129,131-135]
preempt        up   infinite      2   down mpi-n[3,61]
preempt_ndr    up   infinite      1 alloc* mpi-n145
preempt_ndr    up   infinite      4  down* mpi-n[137-138,142,153]
preempt_ndr    up   infinite     13  alloc mpi-n[136,139-141,143-144,146-152]

CLUSTER: smp
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
smp*         up   infinite      1 drain* smp-n199
smp*         up   infinite      1  down* smp-n162
smp*         up   infinite      1   drng smp-n214
smp*         up   infinite      2  drain smp-n[219,237]
smp*         up   infinite     22    mix smp-n[159,170,173,192,209-210,220-223,228-231,233-236,242,249,251,266]
smp*         up   infinite     20  alloc smp-n[175,195,215-218,225-227,232,238-241,243-248]
smp*         up   infinite     47   idle smp-n[156-158,160-161,163-169,171-172,174,176-191,193-194,196-198,200-208,224,250]
high-mem     up   infinite      1  down* smp-1024-n0
high-mem     up   infinite      9    mix smp-1024-n[1-5,7-8],smp-2048-n[0-1]
high-mem     up   infinite      1  alloc smp-1024-n6
preempt      up   infinite      1 drain* smp-n199
preempt      up   infinite      2  down* smp-1024-n0,smp-n162
preempt      up   infinite      1   drng smp-n214
preempt      up   infinite      2  drain smp-n[219,237]
preempt      up   infinite     41    mix smp-1024-n[1-5,7-8],smp-2048-n[0-1],smp-n[159,170,173,192,209-210,220-223,228-231,233-236,242,249,251,253,256-264,266]
preempt      up   infinite     29  alloc osiris-n[0-1],smp-1024-n6,smp-n[175,195,211-212,215-218,225-227,232,238-241,243-248,252,254-255,265]
preempt      up   infinite     47   idle smp-n[156-158,160-161,163-169,171-172,174,176-191,193-194,196-198,200-208,224,250]
pliu         up   infinite     10    mix smp-n[253,256-264]
pliu         up   infinite      5  alloc smp-n[211-212,252,254-255]
osiris       up   infinite      3  alloc osiris-n[0-1],smp-n265
[gnowmik@login1 ~]$
```

Reading the output:

- The **`*` next to a partition name** (`a100*`, `smp*`, `htc*`, `mpi*`) marks
  the cluster's default partition.
- The **`STATE`** column describes the nodes: `idle` (free), `mix` (partly in
  use), `alloc` (fully in use), `drain`/`drng` (not accepting new jobs, e.g. for
  maintenance), and `down` (offline). A `*` *after* a state (e.g. `idle*`) means
  the node isn't currently responding.
- Partitions named after a person (e.g. `pliu`, `osiris`, `isenocak`) are
  **PI-owned investment hardware** and are restricted to those groups. Choose a
  cluster and partition for your work from the
  [**Hardware Profiles**](../hardware_profiles/index.md) section.

!!! tip "The `preempt` partition"
    Every cluster has a `preempt` partition. Jobs there incur **no Service Unit
    cost** but are **preemptible** — Slurm can interrupt them when higher-priority
    paid work needs the nodes. See
    [**Preemptible Partitions**](preempt.md) for how to use it safely.

## Checking the queue with `squeue`

`squeue` lists queued and running jobs. Restrict it to your jobs on a cluster
with `-u $USER`:

```bash
squeue -M gpu -u $USER
```

The `ST` column reports each job's state. The most common values:

| Code | State | Description |
| ---- | ----- | ----------- |
| `PD` | Pending | Awaiting resource allocation |
| `R` | Running | Currently has an allocation and is executing |
| `CG` | Completing | Finishing; some processes may still be active |
| `CD` | Completed | Terminated successfully (exit code 0) |
| `F` | Failed | Terminated with a non-zero exit code |
| `CA` | Cancelled | Cancelled by the user or an administrator |
| `TO` | Timeout | Terminated for reaching its time limit |

See `man squeue` or the [Slurm job state codes](https://slurm.schedmd.com/squeue.html#SECTION_JOB-STATE-CODES)
for the full list. To see when pending jobs are expected to start, run
`squeue --start -j <JobID>`. Note that not all jobs have a definite start time.

## Inspecting a job with `scontrol`

For the full record of a pending or running job:

```bash
scontrol -M <cluster> show job <JobID>
```

A worked example of reading `scontrol` output is in
[**Managing Jobs**](../getting-started/step3/getting-started-step3-manage-jobs.md),
which also covers submitting, monitoring, and cancelling jobs end to end.

## Where to go next

<div class="grid cards" markdown>

-   :material-console:{ .lg .middle } __Work interactively__

    ---

    Grab a compute node for testing and exploration.

    [:octicons-arrow-right-24: Interactive Jobs](interactive-jobs.md)

-   :material-file-document-edit:{ .lg .middle } __Submit a batch job__

    ---

    Write a script that runs unattended, with common directives explained.

    [:octicons-arrow-right-24: Batch Jobs](batch-jobs.md)

-   :material-currency-usd:{ .lg .middle } __Understand the cost__

    ---

    How Service Units are calculated and charged.

    [:octicons-arrow-right-24: Service Units](service-units.md)

</div>
