# Preemptible Partitions

!!! info "Formerly the “scavenger” partitions"
    CRCD's preemptible partitions were previously called **scavenger**. Each
    cluster now has a partition named **`preempt`** (plus **`preempt_ndr`** on
    MPI) that serves the same purpose. If you have older scripts that use
    `--partition=scavenger`, switch them to `--partition=preempt`.

## What the preempt partitions are

Each cluster has a `preempt` partition that lets you use **idle** resources at
**no Service Unit cost**. In exchange, the jobs are **preemptible**: they run
only when resources would otherwise sit idle, and Slurm will stop them when
higher-priority paid jobs need the nodes.

- **Zero cost** — the `preempt` partitions have Service Unit billing weights of
  `0` (see [Service Units](service-units.md)).
- **On every cluster** — SMP, HTC, MPI (`preempt` and `preempt_ndr`), and GPU.
- **Preemptible** — a preempted job is cancelled by default, or requeued if you
  ask for it (see [below](#preemption-and-requeueing)).

## Do you need preempt?

For most workloads, a regular allocation is simpler than working around
preemption. A **Standard Allocation of 25,000 service units per year** is
available to every faculty account and is usable on any cluster, which is enough
to benchmark workflows and prepare a larger [Resource Allocation
Request](https://crc.pitt.edu/service-request-forms/compute-allocation-guidelines).

Reach for `preempt` when you specifically want to run on idle capacity without
drawing down SUs, *and* your work tolerates being interrupted — that is, it fits
one of the patterns below.

## Good use cases

Any preempt job must assume it can be stopped at any time, so it should save
partial output or report failures before it's cancelled. Two patterns fit well:

**Many short jobs.** Short jobs lose little work if preempted. By default
preempted jobs are not requeued, so you decide when and where to resubmit —
either with your own resubmission script, or by requeueing (at the risk of
repeated preemption).

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
[interactive job](interactive-jobs.md).

!!! warning "Confirm the current submission details"
    The `preempt` partitions are relatively new and replaced scavenger during
    cluster maintenance. Before relying on this in production, confirm the
    current specifics with CRCD, as they may differ from the retired scavenger
    setup:

    - the exact partition name(s) on each cluster (`preempt`, `preempt_ndr`);
    - how to target a **GPU type** on the GPU `preempt` partition — the old
      `--constraint=gtx1080|titanx|k40|v100` values refer to retired hardware and
      no longer apply (see the current [GPU cluster](../hardware_profiles/gpu.md)
      partitions such as `a100` and `l40s`);
    - any per-QoS **time limits**, which now appear to apply to `preempt` (unlike
      the old scavenger partitions) — see the
      [Job Scheduling Policy](../policies/job-scheduling-policy.md).

## Preemption and requeueing

If a higher-priority job needs the nodes, a preempt job is **cancelled** by
default. To have Slurm return it to the queue instead, add:

```bash
#SBATCH --requeue
```

A requeued job may be preempted repeatedly, so pair `--requeue` with
checkpointing so each restart resumes rather than starts over.

If a preempt job is *not* being preempted when you'd expect it to be, open a
[support ticket](https://crc.pitt.edu/tickets) with the details.

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
