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

| Partition | Nodes | GPU                        | VRAM  | GPU/Node | --constraint | CPU                                 | Cores/Node | Mem/Node | Scratch     | Network | Node Names                                         |
|-----------|-------|----------------------------|-------|----------|--------------|-------------------------------------|------------|----------|-------------|---------|----------------------------------------------------|
| cpu       | 48    | N/A                        | N/A   | N/A      | (null)       | Intel Xeon Gold 6126                | 24         | 192 GB   | 480 GB NVMe | 1GbE    | teach-cpu-n[0-1,10-19,2,20-29,3,30-39,4,40-47,5-9] |
| gpu       | 1     | N/A                        | N/A   | N/A      | gtx1080      | unknown                             | 19         | 128 GB   | ?           | 10GbE   | teach-gpu-n12                                      |
| gpu       | 9     | NVIDIA GeForce GTX 1080 Ti | 11 GB | 4        | gtx1080      | Intel Xeon Silver 4112              | 19         | 96 GB    | 480 GB NVMe | 1GbE    | teach-gpu-n[17-25]                                 |
| gpu       | 7     | NVIDIA GeForce GTX TITAN X | 12 GB | 4        | titanx       | Intel Xeon CPU E5-2620 v3 @ 2.40GHz | 19         | 128 GB   | 960 GB NVMe | 1GbE    | teach-gpu-n[0-6]                                   |
| gpu       | 2     | NVIDIA L4                  | 22 GB | 8        | l4           | Intel Xeon Platinum 8592+           | 19         | 512 GB   | 733 GB NVMe | 10GbE   | teach-gpu-n[15-16]                                 |
| gpu       | 7     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon CPU E5-2620 v3 @ 2.40GHz | 19         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n[10-11,13-14,7-9]                       |