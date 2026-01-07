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

| Partition | Host Architecture                                                                                                                                                              | --constraint        | Nodes | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network | Node Names       |
|-----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|-------|------------|----------|----------|-------------|---------|------------------|
| htc       | [AMD EPYC 9374F (Genoa)](https://www.amd.com/en/products/cpu/amd-epyc-9374f)                                                                                                   | amd, genoa          | 20    | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe | 10GbE   | htc-n\[50-69]    |
|           | [Intel Xeon Platinum 8352Y (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html) | intel, ice_lake     | 18    | 64         | 512 GB   | 8 GB     | 2 TB NVMe   | 10GbE   | htc-n\[32-49]    |
|           | [Intel Xeon Platinum 8352Y (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html) | intel, ice_lake     | 4     | 64         | 1 TB     | 16 GB    | 2 TB NVMe   | 10GbE   | htc-1024-n\[0-3] |
|           | [Intel Xeon Gold 6248R (Cascade Lake)](https://ark.intel.com/content/www/us/en/ark/products/199351/intel-xeon-gold-6248r-processor-35-75m-cache-3-00-ghz.html)                 | intel, cascade_lake | 8     | 48         | 768 GB   | 16 GB    | 960 GB SSD  | 10GbE   | htc-n\[24-31]    |
