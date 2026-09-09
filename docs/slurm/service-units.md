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
total SUs  ≈  max( cores × compute_weight,  memory_GiB × memory_weight,  GPUs × compute_weight )  ×  hours
```

On the SMP and HTC clusters every general-access tier uses a compute weight of
`1.0`, so a CPU job costs **one SU per core-hour as long as it stays within its
partition's memory-per-core**. Only when a job requests *more* memory than a core's
share does the memory term take over and raise the bill — which is usually a sign a
higher-memory tier is the better fit.

!!! example "Three quick examples"
    **CPU job, within the tier** — 4 cores on `smp_8GB` for 2 hours, using the
    tier's default memory (~8 GB/core). Cores and memory are balanced:
    `max(4 × 1.0, ~31 GiB × 0.1275) ≈ 4` per hour, so ≈ **8 SUs** — exactly the
    core-hours.

    **Memory-heavy job** — 1 core but `--mem=64G` (64 GiB) on `smp_8GB` for 2 hours:
    now *memory* dominates, `max(1 × 1.0, 64 × 0.1275) = 8.16` per hour, so
    ≈ **16 SUs**. Using far more memory than a core's share costs about what the
    equivalent cores would.

    **GPU job** — 1 `a100` card with a balanced share of the host (16 cores,
    ~125 GiB) for 3 hours: `max(16 × 0.0625, 125 × 0.00795, 1 × 1.0) = 1` per hour,
    so ≈ **3 SUs**. A whole 4-GPU `a100` node bills 4 per hour; an 8-card `h200` node
    bills 32. Taking a card's GPU but hogging a whole node's cores or memory raises
    the bill toward the whole-node cost — see the GPU note below.

For the exact charge on a *finished* job, use `crc-seff` or `crc-job-stats`
(see [Checking a job's cost](#checking-a-jobs-cost)) rather than estimating.

--8<-- "../hardware_profiles/memory-units.md"

!!! note "GPU billing is per card, but host CPU and memory still count"
    On the GPU cluster each partition's **GPU weight** is the primary charge — 1 per
    card for L40S and A100-40GB, 2 for RTX PRO 6000 and A100-80GB, 4 for H200. The CPU
    and memory weights split the node's host cores and memory evenly across its cards,
    so a job that takes one card with its fair share of cores and memory bills exactly
    the card weight. Because billing is `MAX`, a job that grabs one card but a whole
    node's worth of cores or memory is billed for what it denies others — up to the
    whole node. Request cores/memory in proportion to the cards you use and you pay the
    card weight.
    Every cluster's `preempt` partition has billing weights of `0` — jobs there
    cost no SUs, but are preemptible. See
    [**Preemptible Partitions**](preempt.md).

## TRES billing weights

Partitions on SMP and HTC are named by memory-per-core (`smp_8GB`, `htc_16GB`, …);
the number is the approximate GB of RAM per core. The memory weight is applied per
**GiB**. On the GPU cluster the per-card weight is the primary charge, and the CPU
and memory weights apportion the host evenly across the node's cards (below).

<style>
.crc-specs-wrap {
  overflow-x: auto;
}
.crc-specs {
  border-collapse: collapse;
  border: 0.05rem solid var(--md-typeset-table-color, rgba(0, 0, 0, 0.12));
  table-layout: auto;
  font-size: 0.7rem;
  line-height: 1.4;
  margin: 0.6em 0;
}
.crc-specs th,
.crc-specs td {
  padding: 0.3em 0.6em;
  border: none;
  border-bottom: 0.05rem solid var(--md-typeset-table-color, rgba(0, 0, 0, 0.12));
  text-align: left;
  vertical-align: top;
  white-space: nowrap;
}
.crc-specs thead th {
  font-weight: 700;
  border-bottom-width: 0.1rem;
}
</style>

<div class="crc-specs-wrap" markdown="0">
<table class="crc-specs">
  <thead>
    <tr>
      <th>Cluster</th><th>Partition</th><th>CPU weight</th>
      <th>Memory weight (per GiB)</th><th>GPU weight (per card)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5">smp</td><td>smp_6GB</td><td>1.0</td><td>0.1702</td><td>—</td>
    </tr>
    <tr><td>smp_8GB</td><td>1.0</td><td>0.1275</td><td>—</td></tr>
    <tr><td>smp_12GB</td><td>1.0</td><td>0.0851</td><td>—</td></tr>
    <tr><td>smp_32GB</td><td>1.0</td><td>0.0321</td><td>—</td></tr>
    <tr><td>preempt</td><td>0</td><td>0</td><td>—</td></tr>
    <tr>
      <td rowspan="4">htc</td><td>htc_8GB</td><td>1.0</td><td>0.1275</td><td>—</td>
    </tr>
    <tr><td>htc_12GB</td><td>1.0</td><td>0.0851</td><td>—</td></tr>
    <tr><td>htc_16GB</td><td>1.0</td><td>0.0636</td><td>—</td></tr>
    <tr><td>preempt</td><td>0</td><td>0</td><td>—</td></tr>
    <tr>
      <td rowspan="4">mpi</td><td>ndr</td><td>1.0</td><td>0.0851</td><td>—</td>
    </tr>
    <tr><td>mpi</td><td>1.0</td><td>0.0956</td><td>—</td></tr>
    <tr><td>preempt</td><td>0</td><td>0</td><td>—</td></tr>
    <tr><td>preempt_ndr</td><td>0</td><td>0</td><td>—</td></tr>
    <tr>
      <td rowspan="8">gpu</td><td>a100</td><td>0.0625</td><td>0.00795</td><td>1.0</td>
    </tr>
    <tr><td>a100_multi</td><td>0.0625</td><td>0.00795</td><td>1.0</td></tr>
    <tr><td>a100_nvlink</td><td>0.0625</td><td>0.00803</td><td>1.0</td></tr>
    <tr><td>a100_nvlink_80g</td><td>0.125</td><td>0.01607</td><td>2.0</td></tr>
    <tr><td>l40s</td><td>0.0625</td><td>0.00795</td><td>1.0</td></tr>
    <tr><td>rtx6k</td><td>0.125</td><td>0.01059</td><td>2.0</td></tr>
    <tr><td>h200</td><td>0.25</td><td>0.01058</td><td>4.0</td></tr>
    <tr><td>preempt</td><td>0</td><td>0</td><td>0</td></tr>
  </tbody>
</table>
</div>

!!! note "Why the memory weight isn't simply 1 ÷ the tier number"
    A tier name such as `smp_8GB` is an approximate **base-10** GB figure, for
    readability. The billing weight is applied per **GiB** (base-2) — Slurm's native
    unit — and is set to 1 ÷ (the tier's actual GiB per core). A job that uses
    exactly its tier's memory-per-core is therefore billed the same on memory as on
    cores. The two numbers differ by about 2.4% (the GB/GiB gap), which is why
    `smp_8GB` uses `0.1275` (= 1 ÷ 7.84 GiB) rather than `0.125`. The same tier
    bills identically on SMP and HTC.

!!! note "MPI billing spans nodes"
    MPI jobs use at least two nodes, and each node is billed on the greater of its
    cores or its memory (the `MAX_TRES` rule). Each MPI partition's memory weight is
    set to 1 ÷ its node's memory-per-core (`mpi` = 1 ÷ 10.46 GiB, `ndr` = 1 ÷ 11.75
    GiB), so a fully used node bills exactly its core count. If you request fewer
    cores but more memory per rank — for example half the cores at double the default
    memory — the memory term sets the bill, so you pay for the node footprint you
    actually hold, up to the whole node. You are billed this way on every node your
    job spans.

### Seeing the weights for a partition

The weights live in the cluster configuration. To read them directly (including
investment hardware), use `scontrol -M <cluster> show partition`:

```
[gnowmik@login1 ~]$ scontrol -M htc show partition htc_8GB
PartitionName=htc_8GB
   ...
   TRES=cpu=1152,mem=9280800M,node=18,billing=1156
   TRESBillingWeights=CPU=1.0,Mem=0.1275G
```

??? note "Full `scontrol show partition` output"
    ```
    [gnowmik@login1 ~]$ scontrol -M htc show partition htc_8GB
    PartitionName=htc_8GB
       AllowGroups=ALL AllowAccounts=ALL AllowQos=short,normal,long,...
       AllocNodes=ALL Default=NO QoS=N/A
       DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
       MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED MaxCPUsPerSocket=UNLIMITED
       Nodes=htc-n[32-49]
       PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
       OverTimeLimit=NONE PreemptMode=CANCEL
       State=UP TotalCPUs=1152 TotalNodes=18 SelectTypeParameters=NONE
       JobDefaults=(null)
       DefMemPerCPU=8000 MaxMemPerNode=UNLIMITED
       TRES=cpu=1152,mem=9280800M,node=18,billing=1156
       TRESBillingWeights=CPU=1.0,Mem=0.1275G

    PartitionName=preempt
       ...
       TRESBillingWeights=CPU=0,Mem=0.0G
    ```

    The `preempt` partition's weights are `0`, which turns off charging there. QoS
    and association names follow the new partition scheme.

## Checking a job's cost { #checking-a-jobs-cost }

To display the TRES a job consumed, use `sacct` with `-j <JobID>` and the following format

```bash
--format=User,JobID,Jobname,AllocTRES%40,Elapsed
```
to output the billing components.

```
[gnowmik@login1 ~]$ sacct -X -M htc -j 10722043 --format=User,JobID,Jobname,AllocTRES%40,Elapsed
     User JobID           JobName                                AllocTRES    Elapsed
--------- ------------ ---------- ---------------------------------------- ----------
  gnowmik 10722043     interacti+     billing=67,cpu=67,mem=536000M,node=1   00:39:49
```

## Checking your allocation

To see how many SUs your group has used and has remaining, run `crc-usage`. When
an allocation runs low or expires, submit a new
[Resource Allocation Request](https://crc.pitt.edu/service-request-forms/compute-allocation-guidelines).

!!! note "Cost is not the same as limits"
    Billing weights determine what a job *costs*. Separately, your group has
    *limits* on how much it can use at once (max CPUs/GPUs/memory per QoS). Those
    parameters are on the [**Job Limits & QoS**](job-limits.md#why-is-my-job-pending)
    page, and exceeding them holds jobs in the `PENDING` state, which are not charged
    until resources are allocated to the job and the job changes to the `Running` state.

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
