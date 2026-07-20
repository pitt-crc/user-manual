---
tags:
  - CRC
  - Hardware
  - GPU Computing
  - User Manual
---

# GPU Cluster

The GPU cluster is optimized for workloads requiring GPU acceleration, including machine learning, molecular dynamics
simulations, and large-scale data analysis. The cluster supports CUDA, TensorFlow, PyTorch, and other GPU-accelerated
frameworks. Users who do not require GPU resources are strongly encouraged to use the [MPI](mpi.md) or [SMP](smp.md)
clusters instead.

!!! note "Requesting GPUs"
    A GPU job must request the number of cards with `--gres=gpu:<N>` and target a
    GPU partition — for example `--cluster=gpu --partition=a100 --gres=gpu:1`. Pin
    a specific GPU type or memory size with `--constraint` using the values in the
    table below. Worked examples are on
    [Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)
    and [Batch Jobs](../slurm/batch-jobs.md).

## Specifications

Nodes are grouped by partition, most capable hardware first.

| Partition   | Nodes | GPU                                          | VRAM   | GPU/Node | --constraint    | CPU                        | Cores/Node | Mem/Node | Scratch      | Network   | Node Names   |
| ----------- | ----- | -------------------------------------------- | ------ | -------- | --------------- | -------------------------- | ---------- | -------- | ------------ | --------- | ------------ |
| rtx6k       | 9     | NVIDIA RTX PRO 6000 Blackwell Server Edition | 96 GB  | 8        | rtx6k,96g,amd   | AMD EPYC 9555              | 128        | 1.5 TB   | 7.2 TB NVMe  | HDR200 IB | gpu-n[74-82] |
| h200        | 2     | NVIDIA H200                                  | 141 GB | 8        | h200,141g,intel | Intel Xeon Platinum 8592+  | 128        | 3 TB     | 7.2 TB NVMe  | HDR200 IB | gpu-n[89-90] |
| l40s        | 19    | NVIDIA L40S                                  | 48 GB  | 4        | l40s,48g,intel  | Intel Xeon Platinum 8462Y+ | 64         | 512 GB   | 7.2 TB NVMe  | 10GbE     | gpu-n[55-73] |
| a100        | 10    | NVIDIA A100-PCIE-40GB                        | 40 GB  | 4        | a100,40g,amd    | AMD EPYC 7742              | 64         | 512 GB   | 1.92 TB NVMe | HDR200 IB | gpu-n[35-44] |
| a100        | 2     | NVIDIA A100-PCIE-40GB                        | 40 GB  | 4        | a100,40g,intel  | Intel Xeon Gold 5220R      | 48         | 384 GB   | 960 GB NVMe  | 10GbE     | gpu-n[33-34] |
| a100_multi  | 10    | NVIDIA A100-PCIE-40GB                        | 40 GB  | 4        | a100,40g,amd    | AMD EPYC 7742              | 64         | 512 GB   | 1.92 TB NVMe | HDR200 IB | gpu-n[45-54] |
| a100_nvlink | 2     | NVIDIA A100-SXM4-80GB                        | 80 GB  | 8        | a100,80g,amd    | AMD EPYC 7742              | 128        | 1 TB     | 1.92 TB NVMe | HDR200 IB | gpu-n[31-32] |
| a100_nvlink | 3     | NVIDIA A100-SXM4-40GB                        | 40 GB  | 8        | a100,40g,amd    | AMD EPYC 7742              | 128        | 1 TB     | 12 TB NVMe   | HDR200 IB | gpu-n[28-30] |

## Partition Details

**rtx6k** — The newest GPU partition: eight NVIDIA RTX PRO 6000 (Blackwell) cards per node with 96 GB of memory each,
suited to the largest models and the highest single-node throughput.

**h200** — Eight NVIDIA H200 cards per node with 141 GB of memory each, for the most memory-hungry training and
inference.

**l40s** — Appropriate for AI, simulation, and 3D-modeling workloads that use up to 4 GPUs on a single node and rely on
single- or mixed-precision math. This partition does **not** support double precision (FP64).

**a100** — The default partition on the GPU cluster, appropriate for workflows that use up to 4 GPUs on a single node.
To pin a host-CPU feature (such as an Intel host), add a constraint, for example:

```
#SBATCH --constraint=intel
```

Multiple features can be given as a comma-separated string.

**a100_multi** — For multi-node GPU workflows. Jobs must request a minimum of 2 nodes with 4 GPUs on each node.

**a100_nvlink** — Multi-GPU computation on an NVIDIA HGX platform with 8× A100 cards tightly coupled through an NVLink
switch. To request a particular GPU memory size (such as the 80 GB cards), add a constraint, for example:

```
#SBATCH --constraint=80g
```

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    GPU jobs are billed per card; on the GPU cluster, memory is not billed separately.

    [:octicons-arrow-right-24: Service Units](../slurm/service-units.md)

-   :material-tune:{ .lg .middle } __Request resources__

    ---

    Request GPUs, cores, and time for an interactive or batch job.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-scale-balance:{ .lg .middle } __Limits & priority__

    ---

    Per-group GPU-card limits, QoS walltimes, and how priority is computed.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
