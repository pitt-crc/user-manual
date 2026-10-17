---
tags:
  - CRC
  - Hardware
  - SMP Computing
  - User Manual
---

# SMP Cluster

The SMP cluster is designed for workloads that run on a single server using shared memory parallelism. Each node provides
multiple CPU cores with access to a common memory space, making the cluster well suited for multithreaded applications,
OpenMP codes, and jobs that do not require distributed computing across multiple nodes.

General-access nodes are grouped into partitions by **memory per core**, named `smp_<N>GB`, where `<N>` is roughly the
gigabytes of RAM available to each core. Pick the tier whose memory-per-core matches your job. Memory-hungry work can use
the higher tiers — `smp_32GB` puts up to 2 TB of RAM on a single node.

Every tier bills the same for compute — **one SU per core-hour** — as long as your job stays within that tier's
memory-per-core. Ask for more memory than a core's share and the memory part of the bill can exceed the cores; see
[Service Units](../slurm/service-units.md) for the exact weights and worked examples.

!!! warning "Partition names have changed"
    The old `smp` and `high-mem` partitions have been replaced by the memory-per-core
    tiers below, and some former `high-mem` nodes have moved to the [HTC](htc.md)
    cluster. Update the `--partition` in your job scripts — see
    [**Partition Name Changes**](partition-migration.md) for the full old-to-new mapping.

## Specifications

Nodes are grouped by partition, lowest memory-per-core first.

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
.crc-specs tr.repurposed td {
  text-decoration: line-through;
  color: var(--md-default-fg-color--light, rgba(0, 0, 0, 0.54));
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
      <th>Partition</th><th class="num">Nodes</th><th>--constraint</th><th class="cpu">CPU</th><th>Max SIMD</th>
      <th class="num">Cores/Node</th><th>Mem/Node</th><th>Mem/Core</th><th>Scratch</th>
      <th>Network</th><th>Node Names</th>
    </tr>
  </thead>
  <tbody>
    <tr class="repurposed">
      <td>smp_6GB</td><td class="num">1</td><td>amd,turin</td><td class="cpu">AMD EPYC<br>9755</td><td>AVX-512</td>
      <td class="num">256</td><td>1.5 TB</td><td>6 GB</td><td>3.2 TB</td><td>10GbE</td><td>smp-n266</td>
    </tr>
    <tr>
      <td>smp_8GB</td><td class="num">55</td><td>amd,rome</td><td class="cpu">AMD EPYC<br>7302</td><td>AVX2</td>
      <td class="num">32</td><td>256 GB</td><td>8 GB</td><td>960 GB</td><td>10GbE</td><td>smp-n[156-210]</td>
    </tr>
    <tr>
      <td>smp_12GB</td><td class="num">38</td><td>amd,genoa</td><td class="cpu">AMD EPYC<br>9374F</td><td>AVX-512</td>
      <td class="num">64</td><td>768 GB</td><td>12 GB</td><td>3.2 TB</td><td>10GbE</td><td>smp-n[214-251]</td>
    </tr>
    <tr>
      <td rowspan="2">smp_32GB</td><td class="num">2</td><td>intel,ice_lake</td><td class="cpu">Intel Xeon<br>Platinum 8352Y</td><td>AVX-512</td>
      <td class="num">64</td><td>2 TB</td><td>32 GB</td><td>10.2 TB</td><td>10GbE</td><td>smp-2048-n[0-1]</td>
    </tr>
    <tr>
      <td class="num">1</td><td>amd,naples</td><td class="cpu">AMD EPYC<br>7351</td><td>AVX2</td>
      <td class="num">32</td><td>1 TB</td><td>32 GB</td><td>1 TB</td><td>10GbE</td><td>smp-1024-n0</td>
    </tr>
  </tbody>
</table>
</div>

!!! warning "smp_6GB is being repurposed"
    The single `smp_6GB` node (`smp-n266`) is being removed from general access and
    will be repurposed for the CRCD Kubernetes cluster. Do not target `smp_6GB` in
    new job scripts.

`Mem/Core` is an approximate base-10 figure — see the note below before using it in a job request.

--8<-- "memory-units.md"

## Additional Features

To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```
#SBATCH --constraint=amd,genoa
```

!!! note "A tier can contain more than one CPU type"
    Grouping is by memory-per-core, not by processor, so some tiers mix
    architectures — `smp_32GB`, for instance, has both AMD (naples) and Intel
    (ice_lake) nodes. If your job needs a specific architecture, pin it with a
    `--constraint`.

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    How Service Units are calculated — every tier bills the same per core, and differs only in memory.

    [:octicons-arrow-right-24: Service Units](../slurm/service-units.md)

-   :material-tune:{ .lg .middle } __Request resources__

    ---

    Choose cores, memory, and a partition for an interactive or batch job.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-scale-balance:{ .lg .middle } __Limits & priority__

    ---

    Per-group CPU and memory limits, QoS walltimes, and how priority is computed.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
