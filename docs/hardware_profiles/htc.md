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

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint       | CPU                       | Cores/Node | Mem/Node | Scratch     | Network | Node Names      |
|-----------|-------|-----|------|----------|--------------------|---------------------------|------------|----------|-------------|---------|-----------------|
| htc       | 20    | N/A | N/A  | N/A      | amd,genoa          | Amd Epyc 9374F            | 64         | 768 GB   | 3.2 TB NVMe | 10GbE   | htc-n[50-69]    |
| htc       | 22    | N/A | N/A  | N/A      | amd,turin          | Amd Epyc 9575F            | 128        | 1.5 TB   | 3.2 TB NVMe | 10GbE   | htc-n[70-91]    |
| htc       | 8     | N/A | N/A  | N/A      | intel,cascade_lake | Intel Xeon Gold 6248R     | 48         | 768 GB   | 960 GB NVMe | 10GbE   | htc-n[24-31]    |
| htc       | 18    | N/A | N/A  | N/A      | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 512 GB   | 960 GB NVMe | 10GbE   | htc-n[32-49]    |
| htc       | 4     | N/A | N/A  | N/A      | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 1 TB     | 960 GB NVMe | 10GbE   | htc-1024-n[0-3] |
