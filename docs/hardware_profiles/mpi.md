---
tags:
  - CRC
  - Hardware
  - MPI Computing
  - User Manual
---

# MPI Cluster

The MPI cluster is optimized to support parallel workloads running across many nodes at once. High-speed networking
enables low-latency communication between processes, making the cluster ideal for tightly coupled codes using the Message
Passing Interfaces or other distributed computing frameworks.

The two partitions correspond to the two InfiniBand fabrics, not to memory tiers: **`ndr`** (NDR-200) and **`mpi`**
(HDR200). Choose the partition that matches the interconnect and scale your job needs; a single tightly coupled job runs
within one fabric.

!!! note "Jobs use a minimum of two nodes"
    The MPI cluster requires at least 2 nodes per job. If you regularly run
    single-node workloads, submit to the [SMP](smp.md) cluster instead — an MPI job
    is billed on every node it spans, so padding single-node work onto the MPI
    cluster wastes your allocation.

## Specifications

Nodes are grouped by partition, newest hardware first.

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
      <th>Partition</th><th class="num">Nodes</th><th class="cpu">CPU</th><th>Max SIMD</th>
      <th class="num">Cores/Node</th><th>Mem/Node</th><th>Mem/Core</th><th>Scratch</th>
      <th>Network</th><th>Node Names</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>ndr</td><td class="num">18</td><td class="cpu">AMD EPYC<br>9575F</td><td>AVX-512</td>
      <td class="num">128</td><td>1.5 TB</td><td>12.0 GB</td><td>3.2 TB</td><td>NDR-200</td><td>mpi-n[136-153]</td>
    </tr>
    <tr>
      <td>mpi</td><td class="num">136</td><td class="cpu">Intel Xeon<br>Gold 6342</td><td>AVX-512</td>
      <td class="num">48</td><td>512 GB</td><td>10.7 GB</td><td>1.6 TB</td><td>HDR200</td><td>mpi-n[0-135]</td>
    </tr>
  </tbody>
</table>
</div>

## How MPI jobs are billed

Every node your job uses is billed on the **greater** of its cores or its memory, per hour:

```
SUs per node ≈ max( cores × 1.0,  memory_GiB × memory_weight )  ×  hours
```

Each partition's memory weight is set to 1 ÷ that node's memory-per-core (`mpi` → `0.0956`, `ndr` → `0.0851`), so a node
you use fully bills exactly its core count — 48 for an `mpi` node, 128 for an `ndr` node. You are charged this on every
node the job spans, summed across the job.

!!! tip "Half the cores, double the memory"
    A common pattern is to request half a node's cores so each MPI rank gets about
    double the default memory (for example `--ntasks-per-node=24 --mem-per-cpu=21G`
    on an `mpi` node). Because you're holding most of the node's memory, the memory
    term sets the bill and you are charged for close to the whole node — even though
    only half the cores are in use. That is expected: the other half can't be given
    to anyone else while you hold the memory. If your ranks fit the default
    memory-per-core, just request cores and you're billed on cores.

See [Service Units](../slurm/service-units.md) for the full weight table and worked examples.

--8<-- "hardware_profiles/memory-units.md"

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    How Service Units are calculated — remember an MPI job bills for every node it spans.

    [:octicons-arrow-right-24: Service Units](../slurm/service-units.md)

-   :material-tune:{ .lg .middle } __Request resources__

    ---

    Set nodes, tasks, cores, and time for a multi-node batch job.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-scale-balance:{ .lg .middle } __Limits & priority__

    ---

    Per-group CPU and memory limits, QoS walltimes, and how priority is computed.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
