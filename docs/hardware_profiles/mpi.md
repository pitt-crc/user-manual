---
tags:
  - CRC
  - Hardware
  - MPI Computing
  - User Manual
---

# MPI Cluster

The MPI cluster is optimized to support parallel workloads running across many nodes at once. High-speed networking
enables low-latency communication between processes, making the cluster ideal for tightly coupled codes using message
passing interfaces or other distributed frameworks.

Jobs on the MPI cluster are allocated a minimum of 2 nodes. Users who regularly run single-node workloads should
submit to the [SMP](smp.md) cluster instead, which is specifically designed for single node jobs. While running single node jobs
on MPI is allowed, resources allocated on the unused node(s) will still be count against the user's consumed
service units.

## Specifications

<!--
  Rows ordered newest first (ndr / NDR InfiniBand before mpi / HDR InfiniBand).
  Node ranges verified against slurm.conf: mpi = mpi-n[0-135], ndr = mpi-n[136-153].
-->

| Partition | Nodes | --constraint | CPU                  | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network    | Node Names     |
|-----------|-------|--------------|----------------------|------------|----------|----------|-------------|------------|----------------|
| ndr       | 18    | ndr          | Amd Epyc 9575F       | 128        | 1.5 TB   | 12 GB    | 3.2 TB NVMe | NDR-200 IB | mpi-n[136-153] |
| mpi       | 136   | hdr          | Intel Xeon Gold 6342 | 48         | 512 GB   | 10.7 GB  | 1.6 TB NVMe | HDR200 IB  | mpi-n[0-135]   |
