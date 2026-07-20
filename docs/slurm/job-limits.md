# Job Limits & QoS

This page is the operational reference for **what you're allowed to request**: the
Quality-of-Service (QoS) walltime tiers, the per-group resource caps, and the
concurrent-job and array limits. For how usage is *charged*, see
[**Service Units**](service-units.md); for *why* these limits exist and how job
priority is computed, see the
[**Job Scheduling Policy**](../policies/job-scheduling-policy.md).

!!! note "Values verified against live cluster configuration"
    The numbers below were taken from the live Slurm configuration. Limits change
    as hardware is added or retired — check your own limits any time with
    `sacctmgr show qos` and `crc-usage`, and treat this page as a snapshot.

## QoS walltime tiers

Your job's **maximum walltime** is governed by its QoS. There are four tiers:

| QoS | Max walltime | Priority tier | Available on |
| --- | --- | --- | --- |
| `short` | 1-00:00:00 (1 day) | 13 | all clusters |
| `normal` | 3-00:00:00 (3 days) | 12 | all clusters |
| `long` | 6-00:00:00 (6 days) | 11 | all clusters |
| `long-long` | 21-00:00:00 (21 days) | 10 | **SMP and HTC only** |

Request a walltime with `--time`; it must fit within the tier you're using. The
shorter tiers carry higher scheduling priority — the "QoS factor" you may see
quoted (1.00 / 0.92 / 0.84 / 0.76) is just the priority tier normalized to the
highest, so a `short` job is favored over a `long` one of equal everything else.
Choosing the shortest QoS that fits your job helps it start sooner. Note that
GPU and MPI jobs top out at `long` (6 days); the 21-day `long-long` tier exists
only on SMP and HTC.

## Group resource limits

Limits are enforced **per group (account)**, not per user — everyone in your
group draws from the same pool. Ask for more than the cap and the extra jobs wait
in `PENDING` (see [Why is my job pending?](#why-is-my-job-pending) below) until
your group's running jobs free up room. The caps shrink as the walltime tier
grows, so a 6-day job can claim fewer cores than a 1-day job.

### CPU cores

| Cluster | Partition | Short (1 d) | Normal (3 d) | Long (6 d) | Long-long (21 d) |
| --- | --- | --- | --- | --- | --- |
| SMP | smp | 2304 | 1613 | 1152 | 461 |
| SMP | high-mem | 320 | 224 | 160 | 64 |
| HTC | htc | 2304 | 1613 | 1152 | 461 |
| MPI | mpi | 3264 | 2285 | 1632 | — |

### Memory (GB)

| Cluster | Partition | Short (1 d) | Normal (3 d) | Long (6 d) | Long-long (21 d) |
| --- | --- | --- | --- | --- | --- |
| SMP | smp | 18432 | 12904 | 9216 | 3688 |
| SMP | high-mem | 5120 | 3584 | 2560 | 1024 |
| HTC | htc | 18432 | 12904 | 9216 | 3688 |

### GPU cards

| Partition | Short (1 d) | Normal (3 d) | Long (6 d) |
| --- | --- | --- | --- |
| a100 | 16 | 12 | 8 |
| a100_multi | 32 | 24 | 8 |
| a100_nvlink | 24 | 16 | 8 |
| a100_nvlink_multi | 24 | 16 | 8 |
| l40s | 24 | 16 | 8 |
| rtx6k | 24 | 16 | 8 |
| h200 | 4 | 2 | 1 |

!!! info "Investor and community-owned partitions"
    Groups that have invested in hardware, and PI-owned community partitions,
    have their own QoS with different limits and higher priority. The tables above
    are the general-access limits. See the
    [Job Scheduling Policy](../policies/job-scheduling-policy.md) for how
    community-owned priority works.

## Concurrent-job and array limits

Beyond the resource caps, there are limits on *how many jobs* you can have
accruing priority at once:

- **Per user:** about 100 jobs accruing priority at a time.
- **Per group:** about 500 jobs (higher on SMP and HTC — 125 per user, 625 per group).

Extra jobs stay queued but stop accruing priority until earlier ones finish; the
pending reason shows as `QOSMaxJobsPerUserLimit` or `QOSMaxJobsPerAccountLimit`.

**Job arrays** are capped by `MaxArraySize`: the highest index is **500** on SMP,
MPI, and HTC, and **1001** on GPU (indices run `0` to `MaxArraySize − 1`). Throttle
how many array tasks run at once with the `%N` suffix — for example
`--array=0-999%50` runs at most 50 tasks concurrently. See
[**Job Arrays**](job-arrays.md) for details.

## Annual allocation limits

Separate from the concurrent limits above, each research group's *annual* resource
allocation is capped. Because these figures are maintained by CRCD and updated
periodically, always confirm the current values on the CRCD
[**Research Compute Allocation**](https://www.crcd.pitt.edu/service-request-forms)
page rather than relying on a copy here. As of this writing:

| Cluster / resource | Annual allocation limit |
| --- | --- |
| MPI | 3,200,000 CPU core-hours |
| HTC (for NIH-funded projects) or SMP | 1,500,000 CPU core-hours |
| GPU | 300,000 card-hours |

New groups start with a one-time startup allocation of 50,000 CPU core-hours on
each cluster plus 5 TB of storage. When an allocation is exhausted or expired,
submit a new [Resource Allocation Request](https://crc.pitt.edu/service-request-forms/compute-allocation-guidelines);
check your current usage with `crc-usage`.

## Why is my job pending? { #why-is-my-job-pending }

A job shown as `PD` (pending) hasn't started yet. `squeue` lists a **reason** in
parentheses; the common ones fall into two groups.

**Resource availability and dependencies** — nothing is wrong; the job is waiting
its turn:

| Reason | Meaning | What to do |
| --- | --- | --- |
| `Resources` | The cluster is busy; no resources free yet. | Wait — it runs when resources free up. |
| `Priority` | Higher-priority jobs are ahead of yours. | Wait; see the [scheduling policy](../policies/job-scheduling-policy.md). |
| `Dependency` | Waiting on another job you marked with `--dependency`. | Wait for that job to finish. |
| `DependencyNeverSatisfied` | A job it depends on failed. | Cancel this job — it can never run — and fix the upstream job. |

**Exceeding a usage limit** — the job is asking for more than your group is
allowed at once:

| Reason | Meaning | What to do |
| --- | --- | --- |
| `QOSMaxJobsPerUserLimit`, `QOSMaxJobsPerAccountLimit`, `JobArrayTaskLimit` | Too many of your jobs are already accruing priority. | Wait for some to finish; they'll start automatically. |
| `MaxCpuPerAccount`, `MaxGRESPerAccount`, `MaxTRESPerAccount` | Your group's running jobs already hold the CPU/GPU cap for this partition and tier. | Wait, request fewer resources, or use a shorter QoS with a higher cap. |
| `MaxMemoryPerAccount` | Your group is at its memory cap for this partition and tier. | Wait or request less memory. |
| `AssocGrpBillingMinutes` | Your group's allocation (Service Units) is used up or expired. | Check `crc-usage`; submit a new allocation request. |

If you keep hitting resource caps, check whether your jobs are actually using what
they request — `crc-seff <jobid>` reports CPU and memory efficiency for a finished
job, which helps you right-size future requests:

```
[nlc60@login1 ~] : crc-seff -M mpi 2707801
Job ID: 2707801
Cluster: mpi
State: TIMEOUT (exit code 0)
Cores per node: 48
CPU Efficiency: 99.84% of 4-00:57:36 core-walltime
Memory Utilized: 8.11 GB
Memory Efficiency: 2.16% of 375.00 GB
```

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What it costs__

    ---

    How Service Units are billed from the resources you request.

    [:octicons-arrow-right-24: Service Units](service-units.md)

-   :material-scale-balance:{ .lg .middle } __Why these limits exist__

    ---

    Fair-share priority, walltime policy, and login-node rules.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

-   :material-recycle:{ .lg .middle } __Run at zero SU cost__

    ---

    Preemptible partitions ignore these caps but can be cancelled.

    [:octicons-arrow-right-24: Preemptible Partitions](preempt.md)

</div>
