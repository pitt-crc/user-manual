# Understanding Our Systems

The CRCD operates several HPC clusters, each optimized for different research workloads. Users connect to these systems through a common pool of login nodes, which serve as the primary entry point for submitting jobs. Selecting the appropriate cluster ensures efficient use of resources and reduces wait times in the job queue.

## Available Clusters

| Cluster | Use Case |
|---------|----------|
| [Login Nodes](../hardware_profiles/login.md) | Entry point for submitting jobs and managing files across all clusters |
| [SMP](../hardware_profiles/smp.md) | Single-node workloads using shared memory parallelism |
| [MPI](../hardware_profiles/mpi.md) | Multi-node workloads using MPI or other distributed frameworks |
| [GPU](../hardware_profiles/gpu.md) | Machine learning, AI, and GPU-accelerated simulations |
| [HTC](../hardware_profiles/htc.md) | Health sciences and genomics workflows |
| [Teach](../hardware_profiles/teach.md) | Classroom instruction and coursework |
| [VIZ](../hardware_profiles/viz.md) | Interactive visualization and GUI-based applications |

## System Architecture

All clusters share a common set of login nodes and storage infrastructure. The diagram below illustrates how these systems interconnect:

- **Login Nodes** provide the entry point for all user interactions
- **Compute Clusters** are accessed exclusively through the Slurm job scheduler
- **Storage Systems** (home, project, scratch) are mounted across all clusters

!!! tip
    Choose your cluster based on your workload characteristics. If unsure, start with the [Getting Started](../getting-started.md) guide or consult the [FAQ](../faq.md).
