---
title: SMP Cluster Specifications
tags:
  - CRC
  - Hardware
  - SMP Computing
  - User Manual
---

# SMP Cluster Overview

The SMP nodes are appropriate for programs that are parallelized using the shared memory framework. These nodes are 
similar to your laptop but with more CPU cores and shared memory space between them.

## Key Features

- high memory partition for nodes with up to 3 TB of shared memory

## Specifications

| Partition | Host Architecture                                                                                                                                                              | --constraint      | Nodes   | Cores/Node   | Mem/Node   | Mem/Core   | Scratch       | Network   | Node Names       |
|------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------|---------|--------------|------------|------------|---------------|-----------|------------------|
| smp  | [AMD EPYC 9374F (Genoa)](https://www.amd.com/en/products/cpu/amd-epyc-9374f)                                                                                                   | amd, genoa        | 43      | 64           | 768 GB     | 12 GB      | 3.2 TB NVMe   | 10GbE     | smp-n\[214-256]  |
|      | [AMD EPYC 7302 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7302)                                                                                                      | amd, rome         | 55      | 32           | 256 GB     | 8 GB       | 1 TB SSD      | 10GbE     | smp-n\[156-210]  |
|      |                                                                                                                                                                                |                   |         |              |            |            |               |           |                  |
| high-mem | [Intel Xeon Platinum 8352Y (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html) | intel, ice_lake   | 8       | 64           | 1 TB       | 16 GB      | 10 TB NVMe    | 10GbE     | smp-1024-n\[1-8] |
|      | [Intel Xeon Platinum 8352Y (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html) | intel, ice_lake   | 2       | 64           | 2 TB       | 32 GB      | 10 TB NVMe    | 10GbE     | smp-2048-n\[0-1] |
|      | [AMD EPYC 7351 (Naples)](https://www.amd.com/en/support/cpu/amd-epyc/amd-epyc-7001-series/amd-epyc-7351)                                                                       | amd, naples       | 1       | 32           | 1 TB       | 32 GB      | 1 TB NVMe     | 10GbE     | smp-1024-n0      |
|      | [Intel Xeon E7-8870v4 (Broadwell)](https://www.intel.com/content/www/us/en/products/sku/93801/intel-xeon-processor-e78870-v4-50m-cache-2-10-ghz/specifications.html)           | intel, broadwell  | 4       | 80           | 3 TB       | 38 GB      | 5 TB SSD      | 10GbE     | smp-3072-n\[0-3] |

## Additional Features

 To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```bash
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```bash
#SBATCH --constraint=amd,genoa
```
