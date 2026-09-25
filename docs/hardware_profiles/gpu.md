---
tags:
  - CRC
  - Hardware
  - GPU Computing
  - User Manual
---

# GPU Cluster

The GPU cluster is optimized for software written to take advantage of the inherent parallelism in the GPU
architecture. The cluster supports CUDA, TensorFlow, PyTorch, and other GPU-accelerated frameworks.

!!! note "Requesting GPUs"
    A GPU job must request the number of cards with `--gres=gpu:<N>` and target a
    GPU partition. For example, `--cluster=gpu --partition=a100 --gres=gpu:1`. Pin
    a specific GPU type or memory size with `--constraint` using the values in the
    table below. Worked examples are on
    [Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)
    and [Batch Jobs](../slurm/batch-jobs.md).

## Specifications

Nodes are grouped by partition, most capable hardware first.

<style>
.crc-specs-wrap {
  overflow-x: auto;
}
.crc-specs {
  border-collapse: collapse;
  border: 0.05rem solid var(--md-typeset-table-color, rgba(0, 0, 0, 0.12));
  table-layout: auto;
  font-size: 0.7rem;
  line-height: 1.4;
  margin: 0.6em 0;
}
.crc-specs th,
.crc-specs td {
  padding: 0.3em 0.6em;
  border: none;
  border-bottom: 0.05rem solid var(--md-typeset-table-color, rgba(0, 0, 0, 0.12));
  text-align: left;
  vertical-align: top;
  white-space: nowrap;
}
.crc-specs thead th {
  font-weight: 700;
  border-bottom-width: 0.1rem;
}
.crc-specs .num {
  text-align: center;
}
.crc-specs td.cpu,
.crc-specs th.cpu {
  text-align: center;
  white-space: normal;
  min-width: 6.4rem;
}
</style>

<div class="crc-specs-wrap" markdown="0">
<table class="crc-specs">
  <thead>
    <tr>
      <th>Partition</th><th class="num">Nodes</th><th>GPU</th><th>VRAM</th><th class="num">GPU/Node</th>
      <th>--constraint</th><th class="cpu">CPU</th><th>Max SIMD</th><th class="num">Cores/Node</th>
      <th>Mem/Node</th><th>Scratch</th><th>Network</th><th>Node Names</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>rtx6k</td><td class="num">9</td><td>NVIDIA RTX PRO<br>6000 Blackwell</td><td>96 GB</td><td class="num">8</td>
      <td>rtx6k,96g,amd</td><td class="cpu">AMD EPYC<br>9555</td><td>AVX-512</td><td class="num">128</td>
      <td>1.5 TB</td><td>7.2 TB</td><td>HDR200</td><td>gpu-n[74-82]</td>
    </tr>
    <tr>
      <td>h200</td><td class="num">2</td><td>NVIDIA H200</td><td>141 GB</td><td class="num">8</td>
      <td>h200,141g,intel</td><td class="cpu">Intel Xeon<br>Platinum 8592+</td><td>AVX-512</td><td class="num">128</td>
      <td>3 TB</td><td>7.2 TB</td><td>HDR200</td><td>gpu-n[89-90]</td>
    </tr>
    <tr>
      <td>l40s</td><td class="num">19</td><td>NVIDIA L40S</td><td>48 GB</td><td class="num">4</td>
      <td>l40s,48g,intel</td><td class="cpu">Intel Xeon<br>Platinum 8462Y+</td><td>AVX-512</td><td class="num">64</td>
      <td>512 GB</td><td>7.2 TB</td><td>10GbE</td><td>gpu-n[55-73]</td>
    </tr>
    <tr>
      <td rowspan="2">a100</td><td class="num">10</td><td>NVIDIA A100<br>PCIE-40GB</td><td>40 GB</td><td class="num">4</td>
      <td>a100,40g,amd</td><td class="cpu">AMD EPYC<br>7742</td><td>AVX2</td><td class="num">64</td>
      <td>512 GB</td><td>1.9 TB</td><td>HDR200</td><td>gpu-n[35-44]</td>
    </tr>
    <tr>
      <td class="num">2</td><td>NVIDIA A100<br>PCIE-40GB</td><td>40 GB</td><td class="num">4</td>
      <td>a100,40g,intel</td><td class="cpu">Intel Xeon<br>Gold 5220R</td><td>AVX-512</td><td class="num">48</td>
      <td>384 GB</td><td>960 GB</td><td>10GbE</td><td>gpu-n[33-34]</td>
    </tr>
    <tr>
      <td>a100_multi</td><td class="num">10</td><td>NVIDIA A100<br>PCIE-40GB</td><td>40 GB</td><td class="num">4</td>
      <td>a100,40g,amd</td><td class="cpu">AMD EPYC<br>7742</td><td>AVX2</td><td class="num">64</td>
      <td>512 GB</td><td>1.9 TB</td><td>HDR200</td><td>gpu-n[45-54]</td>
    </tr>
    <tr>
      <td>a100_nvlink_80g</td><td class="num">2</td><td>NVIDIA A100<br>SXM4-80GB</td><td>80 GB</td><td class="num">8</td>
      <td>a100,80g,amd</td><td class="cpu">AMD EPYC<br>7742</td><td>AVX2</td><td class="num">128</td>
      <td>1 TB</td><td>1.9 TB</td><td>HDR200</td><td>gpu-n[31-32]</td>
    </tr>
    <tr>
      <td>a100_nvlink</td><td class="num">3</td><td>NVIDIA A100<br>SXM4-40GB</td><td>40 GB</td><td class="num">8</td>
      <td>a100,40g,amd</td><td class="cpu">AMD EPYC<br>7742</td><td>AVX2</td><td class="num">128</td>
      <td>1 TB</td><td>12 TB</td><td>HDR200</td><td>gpu-n[28-30]</td>
    </tr>
  </tbody>
</table>
</div>

## Partition Details

**rtx6k** — The newest GPU partition: eight NVIDIA RTX PRO 6000 (Blackwell) cards per node with 96 GB of memory each,
suited for moderate size LLMs.

**h200** — Eight NVIDIA H200 SXM cards per node with 141 GB of memory each, for memory-hungry training and
inference. All eight H200 GPUs are connected by NVLink within a node, offering low-latency RDMA.

**l40s** — Appropriate for AI, simulation, and 3D-modeling workloads that use up to 4 GPUs on a single node and rely on
single- or mixed-precision math.

**a100** — The default partition on the GPU cluster, appropriate for workflows that use up to 4 GPUs on a single node.
To pin a host-CPU feature (such as an Intel host), add a constraint, for example:

```
#SBATCH --constraint=intel
```

Multiple features can be given as a comma-separated string.

**a100_multi** — For multi-node GPU workflows. Jobs must request a minimum of 2 nodes with 4 GPUs on each node.

**a100_nvlink** / **a100_nvlink_80g** — Multi-GPU computation on an NVIDIA HGX platform with 8× A100 cards tightly
coupled through an NVLink switch. The 40 GB cards are in **`a100_nvlink`**; the 80 GB cards are in
**`a100_nvlink_80g`** — select the memory size by choosing the partition (the 80 GB cards bill at a higher rate; see
below).

## How GPU jobs are billed

Every node your job touches is billed on the **greatest** of three terms, per hour:

```
SUs per node ≈ max( cores × cpu_weight,  memory_GiB × memory_weight,  cards × gpu_weight )  ×  hours
```

The **GPU weight** is the primary charge and reflects the card's capability:

| GPU | GPU weight (per card) |
| --- | --------------------- |
| L40S, A100-40GB | 1 |
| RTX PRO 6000, A100-80GB | 2 |
| H200 | 4 |

The CPU and memory weights simply split each node's host cores and memory evenly across its cards. So if you request a
card together with its fair share of the host (roughly `cores ÷ cards` cores and `memory ÷ cards` memory), you're billed
exactly the card weight. Because the bill is a `max`, a job that takes **one card but a whole node's worth of cores or
memory** is billed for the node it effectively blocks — request host resources in proportion to the cards you use and
you pay the card weight. A fully used node bills its card count times the card weight (for example, a whole 8-card `h200`
node bills 32 per hour).

See [Service Units](../slurm/service-units.md) for the exact weights.

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    GPU jobs are billed per card by capability; host cores and memory count only if you use more than a card's share.

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
