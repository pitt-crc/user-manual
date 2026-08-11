# Preemptible Partitions

## What the preempt partitions are

Each cluster has a `preempt` partition that lets you use **idle** resources at
**no Service Unit cost**. In exchange, the jobs are **preemptible**: they run
only when resources would otherwise sit idle, and Slurm will stop them when
higher-priority paid jobs need the resource.

- **Zero cost** — the `preempt` partitions have Service Unit billing weights of
  `0` (see [Service Units](service-units.md)).
- **On every cluster** — SMP, HTC, MPI (`preempt` and `preempt_ndr`), and GPU.
- **Preemptible** — a preempted job is cancelled by default, or requeued if you
  ask for it (see the [**How preemption works**](#how-preemption-works) section below).

## Good use cases

Any preempt job must assume it can be stopped at any time, so it should save
partial output or report failures before it's cancelled. Two patterns fit well:

**Many short jobs.** Short jobs lose little work if preempted. By default
preempted jobs are not requeued, so you decide when and where to resubmit —
either with your own resubmission script, or by requeueing.

**Long, restartable jobs.** A long job should checkpoint: write intermediate
output periodically and, on restart, detect the latest checkpoint to resume
from. Submit it with `--requeue` so Slurm puts it back in the queue after
preemption.

## Submitting to a preempt partition

Request the partition on the cluster you want:

```bash
#SBATCH --cluster=<cluster>
#SBATCH --partition=preempt
```

Everything else works like a normal [batch job](batch-jobs.md) or
[interactive job](interactive-jobs.md). The same QoS walltime tiers apply as on
the regular partitions — `short`, `normal`, and `long` on every cluster, plus
`long-long` on SMP and HTC (see [**Job Limits & QoS**](job-limits.md)).

!!! note "Targeting a GPU type"
    The GPU `preempt` partition spans several GPU types on one partition, so you
    can't select a type by partition name alone. Request
    GPUs with `--gres=gpu:<count>` and, if you need a specific
    model, include the feature using the directive `--constraint=<feature>`.
    See the [**GPU cluster hardware profile**](../hardware_profiles/gpu.md)
    for a list of defined constraints.

## How preemption works { #how-preemption-works }

Preemption is QoS-based (`PreemptType=preempt/qos`): when a higher-priority job
needs nodes your preempt job is holding, Slurm reclaims them. The clusters run
with `PreemptMode=cancel` and no grace period, which means a preempted job is
**cancelled immediately** — it does not get a warning signal or time to shut down
cleanly. This is why preempt work must save progress as it goes rather than at the
end.

To have Slurm return a preempted job to the queue instead of leaving it cancelled,
add:

```bash
#SBATCH --requeue
```

A requeued job may be preempted repeatedly, so pair `--requeue` with checkpointing
so each restart resumes rather than starts over.

## Related

<div class="grid cards" markdown>

-   :material-currency-usd-off:{ .lg .middle } __Why it's free__

    ---

    Preempt partitions have zero billing weights.

    [:octicons-arrow-right-24: Service Units](service-units.md)

-   :material-scale-balance:{ .lg .middle } __How priority works__

    ---

    Fair-share, QoS, and why jobs pend or get preempted.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

-   :material-file-document-edit:{ .lg .middle } __Write the job script__

    ---

    Directives, `--requeue`, and checkpointing in a batch script.

    [:octicons-arrow-right-24: Batch Jobs](batch-jobs.md)

</div>
