---
title: TEACH Node Specifications
tags:
  - CRC
  - Hardware
  - Teach
  - User Manual
---

# TEACH Overview

The TEACH cluster make a subset of hardware on the CRCD system available for students and teachers to develop
computational workflows around course materials without competing with research-oriented jobs.

## Key Features

- Consists of both CPU and GPU hardware

## Specifications

| Resource Type | Node Count | CPU Architecture             | Core/Node | CPU Memory (GB) | GPU Card           | No. GPU | GPU Memory (GB) |
|---------------|------------|------------------------------|-----------|-----------------|--------------------|---------|-----------------|
| `CPU`         | 54         | Gold 6126 Skylake 12C 2.6GHz | 24        | 192             | N/A                | N/A     | N/A             |
|               |            |                              |           |                 |                    |         |                 |
| `GPU 1`       | 7          | E5-2620v3 Haswell 6C 2.4GHz  | 12        | 128             | NVIDIA Titan X     | 4       | 12              |
|               |            |                              |           |                 |                    |         |                 |
| `GPU 2`       | 6          | E5-2620v3 Haswell 6C 2.5GHz  | 12        | 128             | NVIDIA GTX 1080    | 4       | 8               | 
|               |            |                              |           |                 |                    |         |                 |
| `GPU 3`       | 10         | Xeon 4112 Skylake 4C 2.6GHz  | 8         | 96              | NVIDIA GTX 1080 Ti | 4       | 11              |
|               |            |                              |           |                 |                    |         |                 | 
| `GPU 4`       | 2          | Xeon Platinum 8502+ 1.9GHz   | 128       | 512             | NVIDIA L4          | 8       | 24              |
