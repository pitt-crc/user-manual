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

| Partition      | Host Architecture                                                                                                                                                    | Nodes | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network       | Node Names      |
|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|------------|----------|----------|-------------|---------------|-----------------|
| `mpi`          | [Intel Xeon Gold 6342 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215276/intel-xeon-gold-6342-processor-36m-cache-2-80-ghz/specifications.html) | 136   | 48         | 512 GB   | 10.6 GB  | 1.6 TB NVMe | HDR200; 10GbE | mpi-n\[0-135]   |
| `ndr`          | [AMD EPYC 9575F](https://www.amd.com/en/products/processors/server/epyc/9005-series/amd-epyc-9575f.html)                                                             | 18   | 128         | 1.5 TB   | 11.2 GB  | 2.9 TB NVMe | NDR200; 10GbE | mpi-n\[136-153] |
| `opa-high-mem` | [Intel Xeon Gold 6132 (Skylake)](https://ark.intel.com/content/www/us/en/ark/products/123541/intel-xeon-gold-6132-processor-19-25m-cache-2-60-ghz.html)              | 36    | 28         | 192 GB   | 6.8 GB   | 500 TB SSD  | OPA; 10GbE    | opa-n\[96-131]  |
