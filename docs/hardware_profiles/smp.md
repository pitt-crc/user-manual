---
tags:
  - CRC
  - Hardware
  - SMP Computing
  - User Manual
---

# SMP Cluster

The SMP cluster is designed for workloads that run on a single node using shared memory parallelism. Each node provides
multiple CPU cores with access to a common memory space, making the cluster well suited for multithreaded applications,
OpenMP codes, and jobs that do not require distributed computing across multiple nodes.

The cluster has two partitions. Most jobs run on the default **`smp`** partition. Jobs that need more memory than those
nodes provide can use the **`high-mem`** partition, whose nodes offer up to 2 TB of RAM on a single node. The two
partitions are billed at different rates (the `high-mem` nodes cost more per core but less per GB of memory) — see
[Service Units](../slurm/service-units.md) for the exact weights.

## Specifications

Nodes are grouped by partition, newest hardware first.

| Partition | Nodes | --constraint    | CPU                       | Cores/Node | Mem/Node | Mem/Core | Scratch       | Network | Node Names      |
| --------- | ----- | --------------- | ------------------------- | ---------- | -------- | -------- | ------------- | ------- | --------------- |
| smp       | 1     | amd,turin       | AMD EPYC 9755             | 256        | 1.5 TB   | 6 GB     | 3.2 TB NVMe   | 10GbE   | smp-n266        |
| smp       | 38    | amd,genoa       | AMD EPYC 9374F            | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe   | 10GbE   | smp-n[214-251]  |
| smp       | 55    | amd,rome        | AMD EPYC 7302             | 32         | 256 GB   | 8 GB     | 960 GB NVMe   | 10GbE   | smp-n[156-210]  |
| high-mem  | 2     | intel,ice_lake  | Intel Xeon Platinum 8352Y | 64         | 2 TB     | 32 GB    | 10.24 TB NVMe | 10GbE   | smp-2048-n[0-1] |
| high-mem  | 8     | intel,ice_lake  | Intel Xeon Platinum 8352Y | 64         | 1 TB     | 16 GB    | 10.24 TB NVMe | 10GbE   | smp-1024-n[1-8] |
| high-mem  | 1     | amd,naples      | AMD EPYC 7351             | 32         | 1 TB     | 32 GB    | 1 TB NVMe     | 10GbE   | smp-1024-n0     |

## Additional Features

To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```
#SBATCH --constraint=amd,genoa
```

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    How Service Units are calculated — the `smp` and `high-mem` partitions bill differently.

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
