---
title: GPU Cluster Specifications
tags:
  - CRC
  - Hardware
  - GPU Computing
  - User Manual
---

# GPU Cluster Overview

The GPU cluster is optimized for computational tasks requiring GPU acceleration, such as machine learning, molecular dynamics, and large-scale data analysis. Below are the details for the GPU cluster available at the CRC:

## Key Features

- Designed for high-performance GPU workloads.
- Supports CUDA, TensorFlow, PyTorch, and other GPU-accelerated frameworks.
- Integrated with the CRC high-speed network infrastructure.

## Specifications

| Partition     | Nodes  | GPU Type                                                                                                    | GPU/Node  | --constraint     | Host Architecture                                                                                                                                                             | Core/Node   | Max Core/GPU  | Mem/Node   | Mem/Core  | Scratch    | Network           | Nodes        |
|---------------|--------|-------------------------------------------------------------------------------------------------------------|-----------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|---------------|------------|-----------|------------|-------------------|--------------|
| `l40s`        | 20     | [L40S 48GB](https://www.nvidia.com/en-us/data-center/l40s/)                                                 | 4         | `l40s,48g,intel` | [Intel Xeon Platinum 8462Y+](https://www.intel.com/content/www/us/en/products/sku/232383/intel-xeon-platinum-8462y-processor-60m-cache-2-80-ghz/specifications.html)          | 64          | 16            | 512 GB     | 8 GB      | 7 TB NVMe  | 10GbE             | gpu-n[55-74] |
| `a100`        | 10     | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 64          | 16            | 512 GB     | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n[35-44] |
|               | 2      | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,intel` | [Intel Xeon Gold 5220R (Cascade Lake)](https://www.intel.com/content/www/us/en/products/sku/199354/intel-xeon-gold-5220r-processor-35-75m-cache-2-20-ghz/specifications.html) | 48          | 12            | 384 GB     | 8 GB      | 1 TB NVMe  | 10GbE             | gpu-n[33-34] |
| `a100_multi`  | 10     | [A100 40GB PCIe](https://www.nvidia.com/en-us/data-center/a100/)                                            | 4         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 64          | 16            | 512 GB     | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n[45-54] |
| `a100_nvlink` | 2      | [A100 80GB SXM](https://www.nvidia.com/en-us/data-center/a100/)                                             | 8         | `a100,80g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 128         | 16            | 1 TB       | 8 GB      | 2 TB NVMe  | HDR200; 10GbE     | gpu-n[31-32] |
|               | 3      | [A100 40GB SXM](https://www.nvidia.com/en-us/data-center/a100/)                                             | 8         | `a100,40g,amd`   | [AMD EPYC 7742 (Rome)](https://www.amd.com/en/products/cpu/amd-epyc-7742)                                                                                                     | 128         | 16            | 1 TB       | 8 GB      | 12 TB NVMe | HDR200; 10GbE     | gpu-n[28-30] |
| `gtx1080`     | 9      | [GTX 1080 Ti 11GB](https://www.nvidia.com/en-gb/geforce/graphics-cards/geforce-gtx-1080-ti/specifications/) | 4         |                  | [Intel Xeon Silver 4112 Processor](https://ark.intel.com/content/www/us/en/ark/products/123551/intel-xeon-silver-4112-processor-8-25m-cache-2-60...)                          |             |               |            |           |            |                   |              |

