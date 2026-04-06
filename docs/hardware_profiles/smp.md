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

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint   | CPU                       | Cores/Node | Mem/Node | Scratch     | Network | Node Names                      |
|-----------|-------|-----|------|----------|----------------|---------------------------|------------|----------|-------------|---------|---------------------------------|
| smp       | 94    | N/A | N/A  | N/A      | amd,rome       | Amd Epyc 7302             | 47         | 256 GB   | 960 GB NVMe | 10GbE   | smp-n[156-210,214-251,266]      |
| high-mem  | 11    | N/A | N/A  | N/A      | intel,ice_lake | Intel Xeon Platinum 8352Y | 61         | 1 TB     | 10 TB NVMe  | 10GbE   | smp-1024-n[0-8],smp-2048-n[0-1] |

## Additional Features

 To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```bash
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```bash
#SBATCH --constraint=amd,genoa
```
