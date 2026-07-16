---
tags:
  - CRC
  - Hardware
  - HTC Computing
  - User Manual
---

# HTC Cluster

The HTC cluster is designed for data-intensive health science workflows such as genomics and neuroimaging. Jobs run on
single nodes and are well suited for high-throughput pipelines that process many independent tasks in parallel.

Resource allocation on HTC is prioritized for projects funded by the National Institute of Health (NIH). Non-NIH
projects may also use the cluster, but users who are not running biomedical workloads or do not require hardware
specific to the HTC cluster are encouraged to use the [MPI](mpi.md) or [SMP](smp.md) clusters instead.

## Specifications

<!--
  Rows ordered newest CPU generation first (Turin > Genoa > Ice Lake > Cascade Lake).
  Node ranges verified against slurm.conf: the htc partition holds htc-n[24-91] and
  htc-1024-n[0-3]; htc-n92/n93 are investor nodes and excluded.
-->

| Partition | Nodes | --constraint       | CPU                       | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network | Node Names      |
|-----------|-------|--------------------|---------------------------|------------|----------|----------|-------------|---------|-----------------|
| htc       | 22    | amd,turin          | Amd Epyc 9575F            | 128        | 1.5 TB   | 12 GB    | 3.2 TB NVMe | 10GbE   | htc-n[70-91]    |
| htc       | 20    | amd,genoa          | Amd Epyc 9374F            | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe | 10GbE   | htc-n[50-69]    |
| htc       | 4     | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 1 TB     | 16 GB    | 960 GB NVMe | 10GbE   | htc-1024-n[0-3] |
| htc       | 18    | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 512 GB   | 8 GB     | 960 GB NVMe | 10GbE   | htc-n[32-49]    |
| htc       | 8     | intel,cascade_lake | Intel Xeon Gold 6248R     | 48         | 768 GB   | 16 GB    | 960 GB NVMe | 10GbE   | htc-n[24-31]    |
