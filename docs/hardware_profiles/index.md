---
hide:
  - toc
---

# Hardware Profiles

The CRCD operates several HPC clusters, each optimized for a different use case,
plus the nodes that you connect through and the storage tiers that hold your data. You
submit work to whichever cluster best fits your job. Choosing the appropriate one ensures that
resources are used efficiently and reduces the time spent waiting in the queue. This section
profiles each of them.

## Compute Clusters

Submit jobs to these with the `--cluster` directive (or the matching
`crc-interactive` flag). Each page lists the node hardware, partitions, and any
group-specific investment hardware.

| Cluster           | Best for                                                                                                  |
|-------------------|-----------------------------------------------------------------------------------------------------------|
| [SMP](smp.md)     | Single-node jobs using shared-memory parallelism.                                                         |
| [HTC](htc.md)     | Many independent single-node jobs (high-throughput); prioritized for NIH-funded and health sciences work. |
| [MPI](mpi.md)     | Tightly coupled multi-node jobs using the Message Passing Interface or similar frameworks.                |
| [GPU](gpu.md)     | GPU-accelerated work such as AI, machine learning, and physics-based simulation and modeling.             |
| [Teach](teach.md) | Instructional activities, such as coursework, tutorials and workshops, and hackathons.                    |

## Access & Auxiliary Nodes

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

## Storage

Compute is only half the picture — your data needs somewhere to live. CRCD offers
tiered storage allocated per research group, from fast all-flash space for active
jobs to low-cost archive. See [**Storage Tiers**](storage.md) for what each tier
costs and how to request more.

## Choosing a cluster

Once you know which cluster fits, see [**Service Units**](../slurm/service-units.md)
for what a job costs, and the [**Job Scheduling Policy**](../policies/job-scheduling-policy.md)
for per-group limits and how priority is computed. New to the clusters? Start with
[**Getting Started**](../getting-started/index.md).
