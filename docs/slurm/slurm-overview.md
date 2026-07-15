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
    cluster(s) to act on — any of `smp`, `htc`, `mpi`, `gpu` (comma-separated),
    or `all`. Without it, commands use the primary cluster of the login node
    you're on: **SMP** on `h2p.crc.pitt.edu`, **HTC** on `htc.crc.pitt.edu`.

## Viewing cluster status with `sinfo`

`sinfo` reports the partitions on each cluster and the state of their nodes. The
output below is abbreviated to the general-access partitions:

```
[kimwong@login1.crc.pitt.edu ~]$sinfo -M mpi,smp,htc,gpu
CLUSTER: gpu
PARTITION         AVAIL  TIMELIMIT  NODES  STATE NODELIST
a100*                up   infinite     17    mix gpu-n[35-36,38-41,44-54]
a100*                up   infinite      3   idle gpu-n[37,42-43]
l40s                 up   infinite     15    mix gpu-n[55-58,60-62,66-73]
h200                 up   infinite      2    mix gpu-n[89-90]
preempt              up   infinite     37    mix gpu-n[45-58,60-62,66-73,76-80,82,86-90],mems-n0
#   (rtx6k, a100_multi, a100_nvlink*, and PI-owned partitions omitted)

CLUSTER: htc
PARTITION  AVAIL  TIMELIMIT  NODES  STATE NODELIST
htc*          up   infinite      7   idle htc-n[48-49,53,56,86-87,90]
preempt       up   infinite      7   idle htc-n[48-49,53,56,86-87,90]

CLUSTER: mpi
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
mpi*           up   infinite      1    mix mpi-n135
ndr            up   infinite      7   idle mpi-n[136,141,146-150]
preempt        up   infinite      1    mix mpi-n135

CLUSTER: smp
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
smp*         up   infinite     15    mix smp-n[165,184,218,223,226,229,232,234-236,243,246-248,251]
high-mem     up   infinite      2   idle smp-1024-n[5,7]
preempt      up   infinite      4   idle osiris-n[0-1],smp-1024-n[5,7]
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
  [**Hardware Profiles**](../hardware_profiles/overview.md) section.

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
`squeue --start` — though note that not all jobs have a definite start time.

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
