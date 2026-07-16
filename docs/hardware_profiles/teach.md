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

<!--
  Corrected against slurm.conf:
  - teach-cpu-n47 split out: it has 28 cores (CoresPerSocket=14), not 24 like
    teach-cpu-n[0-46].
  - GTX 1080 groupings fixed: slurm.conf has teach-gpu-n[7-12] at 12 cores and
    teach-gpu-n13 alone at 16 cores. The 16-core row previously listed n[12-13]
    (two nodes) while its count said 1.
  - teach-gpu-n14 broken out: slurm.conf lists it with 12 cores and GRES=gpu:4
    but NO GPU-type feature, so its GPU model is unconfirmed.
  Cells marked [confirm] are not in slurm.conf — please fill them.
-->

| Partition | Nodes | GPU                        | VRAM  | GPU/Node | --constraint | CPU                       | Cores/Node | Mem/Node | Scratch     | Network | Node Names                                         |
| --------- |-------| -------------------------- | ----- | -------- |--------------| ------------------------- | ---------- | -------- | ----------- | ------- |----------------------------------------------------|
| cpu       | 47    | N/A                        | N/A   | N/A      | N/A          | Intel Xeon Gold 6126      | 24         | 192 GB   | 480 GB NVMe | 1GbE    | teach-cpu-n[0-46]                                  |
| cpu       | 1     | N/A                        | N/A   | N/A      | N/A          | [confirm model]           | 28         | 192 GB   | [confirm]   | 1GbE    | teach-cpu-n47                                      |
| gpu       | 7     | NVIDIA GeForce GTX TITAN X | 11 GB | 4        | titanx       | Intel Xeon E5-2620 v3     | 12         | 128 GB   | 960 GB NVMe | 1GbE    | teach-gpu-n[0-6]                                   |
| gpu       | 6     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon E5-2620 v3     | 12         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n[7-12]                                  |
| gpu       | 1     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon E5-2620 v4     | 16         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n13                                      |
| gpu       | 1     | [confirm GPU]              | [confirm] | 4     | [confirm]    | [confirm model]           | 12         | 128 GB   | [confirm]   | 1GbE    | teach-gpu-n14                                      |
| gpu       | 2     | NVIDIA L4                  | 24 GB | 8        | l4           | Intel Xeon Platinum 8592+ | 128        | 512 GB   | 733 GB NVMe | 10GbE   | teach-gpu-n[15-16]                                 |
| gpu       | 9     | NVIDIA GeForce GTX 1080 Ti | 11 GB | 4        | gtx1080      | Intel Xeon Silver 4112    | 8          | 96 GB    | 480 GB NVMe | 1GbE    | teach-gpu-n[17-25]                                 |
