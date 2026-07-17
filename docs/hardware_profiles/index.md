---
hide:
  - toc
---

# Our Clusters

The CRCD operates several HPC clusters, each optimized for a different use case.
You connect through a shared pool of **login nodes** and submit work to whichever
cluster best fits your job. Choosing the right one uses resources efficiently and
reduces time spent waiting in the queue.

## Compute clusters

Submit jobs to these with the `--cluster` directive (or the matching
`crc-interactive` flag). Each page lists the node hardware, partitions, and any
group-specific investment hardware.

| Cluster           | Best for                                                                                                  |
|-------------------|-----------------------------------------------------------------------------------------------------------|
| [SMP](smp.md)     | Single-node jobs using shared-memory parallelism.                                                         |
| [HTC](htc.md)     | Many independent single-node jobs (high-throughput); prioritized for NIH-funded and health-sciences work. |
| [MPI](mpi.md)     | Tightly coupled multi-node jobs using MPI or other distributed frameworks.                                |
| [GPU](gpu.md)     | GPU-accelerated work such as machine learning and molecular dynamics.                                     |
| [Teach](teach.md) | Reserved for classroom instruction and coursework.                                                        |

## Access and auxiliary nodes

These are entry points and special-purpose servers — not job-submission targets.

| Node              | Purpose                                                                                          |
|-------------------|--------------------------------------------------------------------------------------------------|
| [Login](login.md) | The shared entry point for submitting jobs and managing files. Not for heavy computation.        |
| [Viz](viz.md)     | Interactive visualization and GUI applications on a Linux desktop.                               |

!!! note "Some partitions are investment hardware"
    A number of partitions are owned by specific research groups who invested in
    the hardware, and are restricted to those groups. Each cluster page notes its
    general-access partitions; see the
    [Hardware Investing Policy](../policies/hardware-investing-policy.md) for how
    investment works.

## Choosing a cluster

Once you know which cluster fits, see [**Service Units**](../slurm/service-units.md)
for what a job costs, and the [**Job Scheduling Policy**](../policies/job-scheduling-policy.md)
for per-group limits and how priority is computed. New to the clusters? Start with
[**Getting Started**](../getting-started/index.md).

## Storage

Compute is only half the picture — your data needs somewhere to live. CRCD offers
tiered storage allocated per research group, from fast all-flash space for active
jobs to low-cost archive. See [**Storage Tiers**](storage.md) for what each tier
costs and how to request more.
