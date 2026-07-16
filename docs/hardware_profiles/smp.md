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

<!--
  Rows ordered newest CPU generation first (Turin > Genoa > Ice Lake > Rome > Naples).
  Node ranges verified against slurm.conf (general smp/high-mem partitions; investor
  nodes such as smp-n[211-212], smp-n[252-265], osiris-* are excluded).
  smp-1024-n0 is a 32-core AMD Naples node (Features=amd,naples), split out from the
  64-core Ice Lake smp-1024-n[1-8]. Cells marked [confirm] are not in slurm.conf —
  please fill the exact CPU model and scratch size for smp-1024-n0.
-->

| Partition | Nodes | --constraint   | CPU                               | Cores/Node | Mem/Node | Mem/Core | Scratch       | Network | Node Names      |
|-----------|-------|----------------|-----------------------------------|------------|----------|----------|---------------|---------|-----------------|
| smp       | 1     | amd,turin      | Amd Epyc 9755                     | 256        | 1.5 TB   | 6 GB     | 3.2 TB NVMe   | 10GbE   | smp-n266        |
| smp       | 38    | amd,genoa      | Amd Epyc 9374F                    | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe   | 10GbE   | smp-n[214-251]  |
| high-mem  | 2     | intel,ice_lake | Intel Xeon Platinum 8352Y         | 64         | 2 TB     | 32 GB    | 10.24 TB NVMe | 10GbE   | smp-2048-n[0-1] |
| high-mem  | 8     | intel,ice_lake | Intel Xeon Platinum 8352Y         | 64         | 1 TB     | 16 GB    | 10.24 TB NVMe | 10GbE   | smp-1024-n[1-8] |
| smp       | 55    | amd,rome       | Amd Epyc 7302                     | 32         | 256 GB   | 8 GB     | 960 GB NVMe   | 10GbE   | smp-n[156-210]  |
| high-mem  | 1     | amd,naples     | AMD EPYC (Naples) [confirm model] | 32         | 1 TB     | 32 GB    | [confirm]     | 10GbE   | smp-1024-n0     |

## Additional Features

 To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```bash
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```bash
#SBATCH --constraint=amd,genoa
```
