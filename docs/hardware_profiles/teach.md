---
tags:
  - CRC
  - Hardware
  - Teach
  - User Manual
---

# TEACH Cluster

The Teach cluster provides dedicated resources for classroom instruction and coursework. It offers a stable environment
for students to learn HPC concepts, run assignments, and develop workflows without competing for resources with
production research workloads. The Teach cluster is not suitable for research work, and should only be used to run
jobs associated with classroom activities.

## Specifications

| Partition | Nodes | GPU                        | VRAM  | GPU/Node | --constraint | CPU                       | Cores/Node | Mem/Node | Scratch     | Network | Node Names                                         |
| --------- |-------| -------------------------- | ----- | -------- |--------------| ------------------------- | ---------- | -------- | ----------- | ------- |----------------------------------------------------|
| cpu       | 48    | N/A                        | N/A   | N/A      | N/A          | Intel Xeon Gold 6126      | 24         | 192 GB   | 480 GB NVMe | 1GbE    | teach-cpu-n[0-1,10-19,2,20-29,3,30-39,4,40-47,5-9] |
| gpu       | 7     | NVIDIA GeForce GTX TITAN X | 11 GB | 4        | titanx       | Intel Xeon E5-2620 v3     | 12         | 128 GB   | 960 GB NVMe | 1GbE    | teach-gpu-n[0-6]                                   |
| gpu       | 6     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon E5-2620 v3     | 12         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n[10-11,14,7-9]                          |
| gpu       | 1     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon E5-2620 v4     | 16         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n[12-13]                                 |
| gpu       | 2     | NVIDIA L4                  | 24 GB | 8        | l4           | Intel Xeon Platinum 8592+ | 128        | 512 GB   | 733 GB NVMe | 10GbE   | teach-gpu-n[15-16]                                 |
| gpu       | 9     | NVIDIA GeForce GTX 1080 Ti | 11 GB | 4        | gtx1080      | Intel Xeon Silver 4112    | 8          | 96 GB    | 480 GB NVMe | 1GbE    | teach-gpu-n[17-25]                                 |
