---
tags:
  - CRC
  - Hardware
  - Teach
  - User Manual
---

# Teach Cluster

The Teach cluster provides dedicated resources for classroom instruction and coursework. It offers a stable environment
for students to learn HPC concepts, run assignments, and develop workflows without competing for resources with
production research workloads. The Teach cluster is not suitable for research work, and should only be used to run
jobs associated with classroom activities.

## Specifications

Nodes are grouped by partition, in node-number order.

| Partition | Nodes | GPU                        | VRAM  | GPU/Node | --constraint | CPU                       | Cores/Node | Mem/Node | Scratch     | Network | Node Names         |
| --------- | ----- | -------------------------- | ----- | -------- | ------------ | ------------------------- | ---------- | -------- | ----------- | ------- | ------------------ |
| cpu       | 47    | N/A                        | N/A   | N/A      | N/A          | Intel Xeon Gold 6126      | 24         | 192 GB   | 480 GB NVMe | 1GbE    | teach-cpu-n[0-46]  |
| cpu       | 1     | N/A                        | N/A   | N/A      | N/A          | Intel Xeon Gold 6132      | 28         | 192 GB   | 480 GB NVMe | 1GbE    | teach-cpu-n47      |
| gpu       | 7     | NVIDIA GeForce GTX TITAN X | 11 GB | 4        | titanx       | Intel Xeon E5-2620        | 12         | 128 GB   | 960 GB NVMe | 1GbE    | teach-gpu-n[0-6]   |
| gpu       | 8     | NVIDIA GeForce GTX 1080    | 8 GB  | 4        | gtx1080      | Intel Xeon E5-2620        | 12         | 128 GB   | 822 GB NVMe | 1GbE    | teach-gpu-n[7-14]  |
| gpu       | 2     | NVIDIA L4                  | 24 GB | 8        | l4           | Intel Xeon Platinum 8592+ | 128        | 512 GB   | 733 GB NVMe | 10GbE   | teach-gpu-n[15-16] |
| gpu       | 9     | NVIDIA GeForce GTX 1080 Ti | 11 GB | 4        | gtx1080      | Intel Xeon Silver 4112    | 8          | 96 GB    | 480 GB NVMe | 1GbE    | teach-gpu-n[17-25] |

## Additional Features

To request a particular feature (such as a specific GPU type), add the following directive to your job script:

```
#SBATCH --constraint=l4
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces).

## Related

<div class="grid cards" markdown>

-   :material-school:{ .lg .middle } __Access for courses__

    ---

    Notebooks on the Teach cluster for classroom use, in your browser.

    [:octicons-arrow-right-24: JupyterHub on Teach](../getting-started/jupyter-teach.md)

-   :material-tune:{ .lg .middle } __Request resources__

    ---

    Choose cores, memory, GPUs, and time for an interactive or batch job.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-scale-balance:{ .lg .middle } __Limits & priority__

    ---

    QoS walltimes, per-group limits, and how priority is computed.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
