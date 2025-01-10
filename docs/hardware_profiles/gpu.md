---
title: GPU Cluster Specifications
tags:
  - CRC
  - Hardware
  - GPU Computing
  - User Manual
---

# GPU Cluster Overview

The GPU cluster is optimized for computational tasks requiring GPU acceleration, such as artificial intelligence and 
machine learning workflows, molecular dynamics simulations, and large-scale data analysis.

## Key Features

- Designed for high-performance GPU workloads.
- Supports CUDA, TensorFlow, PyTorch, and other GPU-accelerated frameworks.

## Specifications

| Partition     | Nodes  | GPU Type                                                                                                    | GPU/Node  | --constraint     | Host Architecture                                                                                                                                                             | Core/Node   | Max Core/GPU  | Mem/Node   | Mem/Core  | Scratch    | Network           | Nodes         |
|---------------|--------|-------------------------------------------------------------------------------------------------------------|-----------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|---------------|------------|-----------|------------|-------------------|---------------|
| `l40s`        | 20     | [L40S 48GB](https://www.nvidia.com/en-us/data-center/l40s/)                                                 | 4         | `l40s,48g,intel` | [Intel Xeon Platinum 8462Y+](https://www.intel.com/content/www/us/en/products/sku/232383/intel-xeon-platinum-8462y-processor-60m-cache-2-80-ghz/specifications.html)          | 64          | 16            | 512 GB     | 8 GB      | 7 TB NVMe  | 10GbE             | gpu-n\[55-74] |
| `a100`        | 10     | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 64          | 16            | 512 GB     | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n\[35-44] |
|               | 2      | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,intel` | [Intel Xeon Gold 5220R (Cascade Lake)](https://www.intel.com/content/www/us/en/products/sku/199354/intel-xeon-gold-5220r-processor-35-75m-cache-2-20-ghz/specifications.html) | 48          | 12            | 384 GB     | 8 GB      | 1 TB NVMe  | 10GbE             | gpu-n\[33-34] |
| `a100_multi`  | 10     | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 64          | 16            | 512 GB     | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n\[45-54] |
| `a100_nvlink` | 2      | [A100 80GB SXM](https://www.nvidia.com/en-us/data-center/a100/)                                             | 8         | `a100,80g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 128         | 16            | 1 TB       | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n\[31-32] |
|               | 3      | [A100 40GB SXM](https://www.nvidia.com/en-us/data-center/a100/)                                             | 8         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 128         | 16            | 1 TB       | 8 GB      | 12 TB NVMe | HDR200; 10GbE     | gpu-n\[28-30] |

## Partition Details

l40s: This partition is appropriate for AI, simulations, 3D modeling workloads that require up to 4x gpus on a single node and rely on single or mixed precision operations (Note: This partition does not support double precision - FP64).
a100: This is the default partition in the gpu cluster and is appropriate for workflows that require up to 4x gpus on a single node. To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

    #SBATCH --constraint=intel

Multiple features can be specified in a comma-separated string.

a100_multi: This partition supports multi-node GPU workflows. Your job must request a minimum of 2 nodes and 4 GPUs on each node.
a100_nvlink: This partition supports multi-GPU computation on an Nvidia HGX platform with 8x A100 that are tightly coupled through an NVLink switch. To request a particular feature (such as an A100 with 80GB of GPU memory), add the the following directive to your job script: 

    #SBATCH --constraint=80g
