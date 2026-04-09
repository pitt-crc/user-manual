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

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint | CPU                  | Cores/Node | Mem/Node | Scratch     | Network    | Node Names                                                                                                                   |
|-----------|-------|-----|------|----------|--------------|----------------------|------------|----------|-------------|------------|------------------------------------------------------------------------------------------------------------------------------|
| mpi       | 136   | N/A | N/A  | N/A      | hdr          | Intel Xeon Gold 6342 | 48         | 512 GB   | 1.6 TB NVMe | HDR200 IB  | mpi-n[0-1,10,100-109,11,110-119,12,120-129,13,130-135,14-19,2,20-29,3,30-39,4,40-49,5,50-59,6,60-69,7,70-79,8,80-89,9,90-99] |
| ndr       | 18    | N/A | N/A  | N/A      | ndr          | Amd Epyc 9575F       | 128        | 1.5 TB   | 3.2 TB NVMe | NDR-200 IB | mpi-n[136-153]                                                                                                               |

