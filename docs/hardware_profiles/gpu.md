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

| Partition   | Nodes | GPU                                          | VRAM   | GPU/Node | --constraint    | CPU                        | Cores/Node | Mem/Node | Scratch    | Network   | Node Names   |
|-------------|-------|----------------------------------------------|--------|----------|-----------------|----------------------------|------------|----------|------------|-----------|--------------|
| a100        | 12    | NVIDIA A100-PCIE-40GB                        | 40 GB  | 4        | a100,40g,amd    | Amd Epyc 7742              | 61         | 512 GB   | 2 TB NVMe  | HDR200 IB | gpu-n[33-44] |
| a100_multi  | 10    | NVIDIA A100-PCIE-40GB                        | 40 GB  | 4        | a100,40g,amd    | Amd Epyc 7742              | 64         | 512 GB   | 2 TB NVMe  | HDR200 IB | gpu-n[45-54] |
| a100_nvlink | 3     | NVIDIA A100-SXM4-40GB                        | 40 GB  | 8        | a100,40g,amd    | Amd Epyc 7742              | 128        | 1 TB     | 12 TB NVMe | HDR200 IB | gpu-n[28-30] |
| a100_nvlink | 2     | NVIDIA A100-SXM4-80GB                        | 80 GB  | 8        | a100,80g,amd    | Amd Epyc 7742              | 128        | 1 TB     | 2 TB NVMe  | HDR200 IB | gpu-n[31-32] |
| l40s        | 19    | NVIDIA L40S                                  | 45 GB  | 4        | l40s,48g,intel  | Intel Xeon Platinum 8462Y+ | 64         | 512 GB   | 7 TB NVMe  | 10GbE     | gpu-n[55-73] |
| rtx6k       | 9     | NVIDIA RTX PRO 6000 Blackwell Server Edition | 96 GB  | 8        | rtx6k,96g,amd   | Amd Epyc 9555              | 128        | 1 TB     | 7 TB NVMe  | HDR200 IB | gpu-n[74-82] |
| h200        | 2     | NVIDIA H200                                  | 140 GB | 8        | h200,141g,intel | Intel Xeon Platinum 8592+  | 128        | 3 TB     | 7 TB NVMe  | HDR200 IB | gpu-n[89-90] |

## Partition Details

l40s: This partition is appropriate for AI, simulations, 3D modeling workloads that require up to 4x gpus on a single node and rely on single or mixed precision operations (Note: This partition does not support double precision - FP64).

a100: This is the default partition in the gpu cluster and is appropriate for workflows that require up to 4x gpus on a single node. To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

    #SBATCH --constraint=intel

Multiple features can be specified in a comma-separated string.

a100_multi: This partition supports multi-node GPU workflows. Your job must request a minimum of 2 nodes and 4 GPUs on each node.

a100_nvlink: This partition supports multi-GPU computation on an Nvidia HGX platform with 8x A100 that are tightly coupled through an NVLink switch. To request a particular feature (such as an A100 with 80GB of GPU memory), add the the following directive to your job script: 

    #SBATCH --constraint=80g
