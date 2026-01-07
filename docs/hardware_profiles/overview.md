# Clusters

The CRCD operates several HPC clusters, each optimized for a different use case. Users can connect these systems using
a common pool of login nodes, which serve as the primary entry point for submitting jobs. Selecting the appropriate
cluster ensures the efficient use of resources and reduces wait times in the job queue.

| Cluster           | Use Case                                                                      |
|-------------------|-------------------------------------------------------------------------------|
| [Login](login.md) | The entry point for submitting jobs and managing files across all clusters.   |
| [SMP](smp.md)     | Designed for single-node workloads using shared memory parallelism.           |
| [MPI](mpi.md)     | Optimized for multi-node workloads using MPI or other distributed frameworks. |
| [GPU](gpu.md)     | GPU-harded for machine learning and molecular dynamics workloads.             |
| [HTC](htc.md)     | Hardware prioritized for NIH-funded projects and health sciences workflows.   |
| [Teach](teach.md) | Legacy hardware used for classroom instruction and coursework.                |
| [VIZ](viz.md)     | Interactive visualization and GUI-based applications.                         |
