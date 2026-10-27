---
tags:
  - CRC
  - Hardware
  - User Manual
---

# Partition Name Changes

The SMP and HTC clusters have moved to partitions named by **memory per core**.
Instead of a few broad partitions (`smp`, `high-mem`, `htc`), each cluster now has a
small set of tiers named `<cluster>_<N>GB`, where `<N>` is roughly the gigabytes of
RAM available to each core.

Why the change:

- **The name tells you the hardware.** `smp_8GB` gives about 8 GB of RAM per core,
  so you choose a partition by the memory your job needs.
- **Billing is consistent across clusters.** The same tier bills at the same rate on
  SMP and HTC — `smp_12GB` and `htc_12GB` cost the same.
- **Simpler cost model.** Every tier bills one SU per core-hour as long as your job
  stays within the tier's memory-per-core. See [Service Units](../slurm/service-units.md).

!!! warning "Update the `--partition` in your job scripts"
    Old partition names are being retired. Jobs that request `smp`, `high-mem`, or
    `htc` will need to name a tier instead. Use the tables below to translate.

## What you need to change

Replace the partition in your `#SBATCH` directives (or `srun`/`crc-interactive`
flags) with the matching tier. The cluster you submit to (`--cluster`) is unchanged,
except for the ice_lake nodes noted below, which moved from SMP to HTC.

```
# before
#SBATCH --cluster=smp
#SBATCH --partition=smp

# after — pick the tier whose memory-per-core fits your job
#SBATCH --cluster=smp
#SBATCH --partition=smp_8GB
```

If you omit `--partition`, your job lands on the cluster's default tier
(`smp_8GB` on SMP). Naming a tier explicitly is recommended.

## SMP

| Old partition | Hardware | New partition |
| ------------- | -------- | ------------- |
| `smp` | turin, AMD EPYC 9755 (256-core) | `smp_6GB` |
| `smp` | rome, AMD EPYC 7302 | `smp_8GB` |
| `smp` | genoa, AMD EPYC 9374F | `smp_12GB` |
| `high-mem` | 2 TB ice_lake, 1 TB naples (32 GB/core) | `smp_32GB` |
| `high-mem` | 1 TB ice_lake (16 GB/core) | **moved to HTC** as [`htc_16GB`](htc.md) |

## HTC

| Old partition | Hardware | New partition |
| ------------- | -------- | ------------- |
| `htc` | ice_lake 512 GB | `htc_8GB` |
| `htc` | turin, genoa | `htc_12GB` |
| `htc` | cascade_lake, ice_lake 1 TB | `htc_16GB` |

## Nodes that moved between clusters

The eight 1 TB ice_lake nodes that were the SMP `high-mem` partition's 16 GB/core
nodes are now on HTC. Both their cluster and their names changed:

| Was (SMP) | Now (HTC) |
| --------- | --------- |
| `smp-1024-n[1-8]` | `htc-1024-n[4-11]` |

If your workflow referred to these nodes by name, or submitted 16 GB/core `high-mem`
jobs to SMP, send those jobs to `--cluster=htc --partition=htc_16GB` instead.

--8<-- "memory-units.md"

## Related

<div class="grid cards" markdown>

-   :material-currency-usd:{ .lg .middle } __What a job costs__

    ---

    Billing weights for every tier, and how the same tier costs the same on both clusters.

    [:octicons-arrow-right-24: Service Units](../slurm/service-units.md)

-   :material-server:{ .lg .middle } __SMP hardware__

    ---

    The `smp_*` tiers and their nodes.

    [:octicons-arrow-right-24: SMP Cluster](smp.md)

-   :material-server:{ .lg .middle } __HTC hardware__

    ---

    The `htc_*` tiers and their nodes.

    [:octicons-arrow-right-24: HTC Cluster](htc.md)

</div>
