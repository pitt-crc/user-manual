---
tags:
  - CRC
  - Hardware
  - HTC Computing
  - User Manual
---

# HTC Cluster

The HTC cluster is designed for data-intensive health science workflows such as genomics and neuroimaging. Jobs run on
single nodes and are well suited for high-throughput pipelines that process many independent tasks in parallel.

Resource allocation on HTC is prioritized for projects funded by the National Institutes of Health (NIH). Non-NIH
projects may also use the cluster, but users who are not running biomedical workloads or do not require hardware
specific to the HTC cluster are encouraged to use the [SMP](smp.md) cluster instead.

General-access nodes are grouped into partitions by **memory per core**, named `htc_<N>GB`, where `<N>` is roughly the
gigabytes of RAM available to each core. Every tier bills the same for compute — **one SU per core-hour** — as long as
your job stays within that tier's memory-per-core; see [Service Units](../slurm/service-units.md) for the exact weights.
The `htc_8GB`, `htc_12GB`, and `htc_16GB` tiers bill at the same rates as the matching `smp_*` tiers on the
[SMP](smp.md) cluster.

!!! warning "Partition names have changed"
    The single `htc` partition has been replaced by the memory-per-core tiers below,
    and `htc_16GB` now also includes eight 1 TB ice_lake nodes moved over from the SMP
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
    <tr>
      <td>htc_8GB</td><td class="num">18</td><td>intel,ice_lake</td><td class="cpu">Intel Xeon<br>Platinum 8352Y</td><td>AVX-512</td>
      <td class="num">64</td><td>512 GB</td><td>8 GB</td><td>960 GB</td><td>10GbE</td><td>htc-n[32-49]</td>
    </tr>
    <tr>
      <td rowspan="2">htc_12GB</td><td class="num">22</td><td>amd,turin</td><td class="cpu">AMD EPYC<br>9575F</td><td>AVX-512</td>
      <td class="num">128</td><td>1.5 TB</td><td>12 GB</td><td>3.2 TB</td><td>10GbE</td><td>htc-n[70-91]</td>
    </tr>
    <tr>
      <td class="num">20</td><td>amd,genoa</td><td class="cpu">AMD EPYC<br>9374F</td><td>AVX-512</td>
      <td class="num">64</td><td>768 GB</td><td>12 GB</td><td>3.2 TB</td><td>10GbE</td><td>htc-n[50-69]</td>
    </tr>
    <tr>
      <td rowspan="2">htc_16GB</td><td class="num">8</td><td>intel,cascade_lake</td><td class="cpu">Intel Xeon<br>Gold 6248R</td><td>AVX-512</td>
      <td class="num">48</td><td>768 GB</td><td>16 GB</td><td>960 GB</td><td>10GbE</td><td>htc-n[24-31]</td>
    </tr>
    <tr>
      <td class="num">12</td><td>intel,ice_lake</td><td class="cpu">Intel Xeon<br>Platinum 8352Y</td><td>AVX-512</td>
      <td class="num">64</td><td>1 TB</td><td>16 GB</td><td>960 GB</td><td>10GbE</td><td>htc-1024-n[0-11]</td>
    </tr>
  </tbody>
</table>
</div>

`Mem/Core` is an approximate base-10 figure — see the note below before using it in a job request.
The `htc-1024-n[4-11]` nodes were previously the SMP `smp-1024-n[1-8]` nodes.

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
    Grouping is by memory-per-core, not by processor. `htc_12GB`, for example,
    holds both turin and genoa nodes. If your job needs a specific architecture,
    pin it with a `--constraint`.

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    How Service Units are calculated and charged against your allocation.

    [:octicons-arrow-right-24: Service Units](../slurm/service-units.md)

-   :material-tune:{ .lg .middle } __Request resources__

    ---

    Choose cores, memory, and time for an interactive or batch job.

    [:octicons-arrow-right-24: Requesting Resources](../getting-started/step3/getting-started-step3-resources.md)

-   :material-scale-balance:{ .lg .middle } __Limits & priority__

    ---

    Per-group CPU and memory limits, QoS walltimes, and how priority is computed.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
