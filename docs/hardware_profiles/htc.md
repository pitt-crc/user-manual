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
specific to the HTC cluster are encouraged to use the [MPI](mpi.md) or [SMP](smp.md) clusters instead.

## Specifications

Nodes are listed newest hardware first.

| Partition | Nodes | --constraint       | CPU                       | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network | Node Names      |
| --------- | ----- | ------------------ | ------------------------- | ---------- | -------- | -------- | ----------- | ------- | --------------- |
| htc       | 22    | amd,turin          | AMD EPYC 9575F            | 128        | 1.5 TB   | 12 GB    | 3.2 TB NVMe | 10GbE   | htc-n[70-91]    |
| htc       | 20    | amd,genoa          | AMD EPYC 9374F            | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe | 10GbE   | htc-n[50-69]    |
| htc       | 4     | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 1 TB     | 16 GB    | 960 GB NVMe | 10GbE   | htc-1024-n[0-3] |
| htc       | 18    | intel,ice_lake     | Intel Xeon Platinum 8352Y | 64         | 512 GB   | 8 GB     | 960 GB NVMe | 10GbE   | htc-n[32-49]    |
| htc       | 8     | intel,cascade_lake | Intel Xeon Gold 6248R     | 48         | 768 GB   | 16 GB    | 960 GB NVMe | 10GbE   | htc-n[24-31]    |

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
