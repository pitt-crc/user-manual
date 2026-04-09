---
tags:
  - CRC
  - Hardware
  - SMP Computing
  - User Manual
---

# SMP Cluster

The SMP cluster is designed for workloads that run on a single node using shared memory parallelism. Each node provides
multiple CPU cores with access to a common memory space, making the cluster well suited for multithreaded applications,
OpenMP codes, and jobs that do not require distributed computing across multiple nodes.

## Specifications

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint   | CPU                       | Cores/Node | Mem/Node | Scratch       | Network | Node Names      |
|-----------|-------|-----|------|----------|----------------|---------------------------|------------|----------|---------------|---------|-----------------|
| smp       | 55    | N/A | N/A  | N/A      | amd,rome       | Amd Epyc 7302             | 32         | 256 GB   | 960 GB NVMe   | 10GbE   | smp-n[156-210]  |
| smp       | 38    | N/A | N/A  | N/A      | amd,genoa      | Amd Epyc 9374F            | 64         | 768 GB   | 3.2 TB NVMe   | 10GbE   | smp-n[214-251]  |
| smp       | 1     | N/A | N/A  | N/A      | amd,turin      | Amd Epyc 9755             | 256        | 1.5 TB   | 3.2 TB NVMe   | 10GbE   | smp-n266        |
| high-mem  | 9     | N/A | N/A  | N/A      | intel,ice_lake | Intel Xeon Platinum 8352Y | 64         | 1 TB     | 10.24 TB NVMe | 10GbE   | smp-1024-n[0-8] |
| high-mem  | 2     | N/A | N/A  | N/A      | intel,ice_lake | Intel Xeon Platinum 8352Y | 64         | 2 TB     | 10.24 TB NVMe | 10GbE   | smp-2048-n[0-1] |

## Additional Features

 To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```bash
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```bash
#SBATCH --constraint=amd,genoa
```
