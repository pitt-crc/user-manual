---
tags:
  - CRC
  - Hardware
  - MPI Computing
  - User Manual
---

# MPI Cluster

The MPI cluster is optimized to support parallel workloads running across many nodes at once. High-speed networking
enables low-latency communication between processes, making the cluster ideal for tightly coupled codes using message
passing interfaces or other distributed frameworks.

!!! note "Jobs use a minimum of two nodes"
    The MPI cluster allocates at least 2 nodes per job. If you regularly run
    single-node workloads, submit to the [SMP](smp.md) cluster instead — it is
    designed for them. Running a single-node job here still reserves the unused
    node(s), and those resources are counted against your service units.

## Specifications

Nodes are grouped by partition, newest hardware first.

| Partition | Nodes | --constraint | CPU                  | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network    | Node Names     |
| --------- | ----- | ------------ | -------------------- | ---------- | -------- | -------- | ----------- | ---------- | -------------- |
| ndr       | 18    | ndr          | AMD EPYC 9575F       | 128        | 1.5 TB   | 12 GB    | 3.2 TB NVMe | NDR-200 IB | mpi-n[136-153] |
| mpi       | 136   | hdr          | Intel Xeon Gold 6342 | 48         | 512 GB   | 10.7 GB  | 1.6 TB NVMe | HDR200 IB  | mpi-n[0-135]   |

## Additional Features

To request a particular feature (such as the newer NDR nodes), add the following directive to your job script:

```
#SBATCH --constraint=ndr
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces).

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    How Service Units are calculated — remember an MPI job bills for every node it reserves.

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
