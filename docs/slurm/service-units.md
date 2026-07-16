# Service Units

A **service unit (SU)** is roughly one core-hour of computing. Jobs draw SUs from
your research group's allocation, based on the resources allocated to the job and
how long it runs.

Slurm tracks these resources as **T**rackable **RES**ources (TRES). The ones that
determine an SU charge are:

- the number of **cores** requested,
- the amount of **memory** (RAM) requested, and
- on the GPU cluster, the number of **GPU cards** requested.

## How a job's cost is calculated

Each TRES has a **billing weight** (below). For a running job, Slurm computes a
billing rate from the *maximum* of the weighted resources — not the sum — and the
total charge is that rate multiplied by the job's walltime:

```
total SUs  ≈  max(cores × compute_weight,  GB × memory_weight,  GPUs × compute_weight)  ×  hours
```

!!! example "Two quick examples (SMP and GPU)"
    **CPU job** — 4 cores and 16 GB on `smp` for 2 hours (weights `0.8`/core,
    `0.102`/GB): the cores dominate, `max(4 × 0.8, 16 × 0.102) = 3.2` per hour,
    so ≈ **6.4 SUs**.

    **Memory-heavy job** — 1 core but 64 GB on `smp` for 2 hours: now *memory*
    dominates, `max(1 × 0.8, 64 × 0.102) = 6.5` per hour, so ≈ **13 SUs**. This is
    why a low-core, high-memory job can cost more than the core count suggests.

    **GPU job** — 1 `a100` card for 3 hours (weight `8`/GPU, memory weight `0`):
    `max(1 × 8, …) = 8` per hour, so ≈ **24 SUs**.

For the exact charge on a *finished* job, use `crc-seff` or `crc-job-stats`
(see [Checking a job's cost](#checking-a-jobs-cost)) rather than estimating.

!!! tip "Zero-cost `preempt` partitions"
    Every cluster's `preempt` partition has billing weights of `0` — jobs there
    cost no SUs, but are preemptible. See
    [**Preemptible Partitions**](preempt.md).

## TRES billing weights

| Cluster | Partition | Compute weight (per CPU/GPU) | Memory weight (per GB) |
| ------- | --------- | ---------------------------- | ---------------------- |
| SMP | smp | 0.8 | 0.102 |
| | high-mem | 1.0 | 0.0477 |
| | preempt | 0 | 0 |
| MPI | ndr | 1 | 0.093 |
| | mpi | 1 | 0.093 |
| | preempt | 0 | 0 |
| | preempt_ndr | 0 | 0 |
| GPU | a100 | 8 | 0 |
| | a100_multi | 8 | 0 |
| | a100_nvlink | 8 | 0 |
| | a100_nvlink_multi | 8 | 0 |
| | l40s | 8 | 0 |
| | preempt | 0 | 0 |
| HTC | htc | 1 | 0.128 |
| | preempt | 0 | 0 |

### Seeing the weights for a partition

The weights live in the cluster configuration. To read them directly (including
investment hardware), use `scontrol show partition` with `-M`:

```
[nlc60@login1 ~] : scontrol -M htc show partition
PartitionName=htc
   ...
   TRES=cpu=6144,mem=72007200M,node=74,billing=9000
   TRESBillingWeights=CPU=1.0,Mem=0.128G
```

??? note "Full `scontrol show partition` output"
    ```
    [nlc60@login1 ~] : scontrol -M htc show partition
    PartitionName=htc
       AllowGroups=ALL AllowAccounts=ALL AllowQos=short,normal,long,htc-htc-s,htc-htc-n,htc-htc-l,htc-htc-ll,htc-htc-s-invest,htc-htc-n-invest,htc-htc-l-invest,htc-htc-ll-invest,htc-htc-crunyan-s,htc-htc-crunyan-n,htc-htc-crunyan-l,htc-htc-crunyan-ll
       AllocNodes=ALL Default=YES QoS=N/A
       DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
       MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED MaxCPUsPerSocket=UNLIMITED
       Nodes=htc-1024-n[0-3],htc-n[24-93]
       PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
       OverTimeLimit=NONE PreemptMode=CANCEL
       State=UP TotalCPUs=6144 TotalNodes=74 SelectTypeParameters=NONE
       JobDefaults=(null)
       DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
       TRES=cpu=6144,mem=72007200M,node=74,billing=9000
       TRESBillingWeights=CPU=1.0,Mem=0.128G

    PartitionName=preempt
       AllowGroups=ALL AllowAccounts=ALL AllowQos=htc-preempt-s,htc-preempt-n,htc-preempt-l,htc-preempt-ll
       AllocNodes=ALL Default=NO QoS=N/A
       ...
       TRES=cpu=6144,mem=72007200M,node=74
       TRESBillingWeights=CPU=0,Mem=0.0G
    ```

## Checking a job's cost { #checking-a-jobs-cost }

For a concise view of the TRES a job used, use `sacct`:

```
[nlc60@login1 ~] : sacct -X -M smp -j 6169876 --format=User,JobID,Jobname,AllocTRES%30,Elapsed
     User        JobID    JobName                      AllocTRES    Elapsed
--------- ------------ ---------- ------------------------------ ----------
    nlc60 6169876      hello_wor+         cpu=1,mem=4018M,node=1   00:00:01
```

The `crc-seff` wrapper reports how *efficiently* a completed job used its
allocation — helpful for right-sizing future requests — and `crc-job-stats`
(added to a batch script) appends a usage summary to the job's output. See
[**CRC Wrappers**](../applications/crc-wrappers.md).

## Checking your allocation

To see how many SUs your group has used and has remaining, run `crc-usage`. When
an allocation runs low or expires, submit a new
[Resource Allocation Request](https://crc.pitt.edu/service-request-forms/compute-allocation-guidelines).

!!! note "Cost is not the same as limits"
    Billing weights determine what a job *costs*. Separately, your group has
    *limits* on how much it can use at once (max CPUs/GPUs/memory per QoS). Those
    are on the [**Job Limits & QoS**](job-limits.md#why-is-my-job-pending)
    page, and exceeding them holds jobs in `PENDING` rather than costing extra.

## Related

<div class="grid cards" markdown>

-   :material-tune:{ .lg .middle } __Choose what to request__

    ---

    Cores, memory, time, and GPUs for interactive and batch jobs.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-recycle:{ .lg .middle } __Run at zero SU cost__

    ---

    Use idle resources on the preemptible partitions.

    [:octicons-arrow-right-24: Preemptible Partitions](preempt.md)

-   :material-scale-balance:{ .lg .middle } __Usage limits__

    ---

    QoS walltimes and per-group CPU, GPU, and memory caps.

    [:octicons-arrow-right-24: Job Limits & QoS](job-limits.md)

</div>
