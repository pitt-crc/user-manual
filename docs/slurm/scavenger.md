# Scavenger Partitions

## Deprecation Notice

The scavenger partitions are being phased out:

- The GPU cluster's scavenger partition has been removed
- New hardware is no longer added to scavenger partitions on other clusters

**Reasons for this change:**

1. **Abundant compute resources are available upon request** - A "Standard Allocation" of 25,000 service units per year is available to all faculty accounts. The proposal process handles nearly all use cases, and the standard allocation amount will be increased to reduce inconvenience.

2. **Simplified job priority** - Scavenger partitions created situations where paid jobs were not preempting scavenger jobs due to priority factor configuration. Removing them simplifies the system and provides more transparency.

---

## Overview

Scavenger partitions allow groups to use up to 90% of cluster resources at no service unit cost, with no time limits. However, jobs on this partition are **preemptible** - they only run when resources are idle and can be cancelled by Slurm when paid jobs need the resources.

## Use Cases

### Many Short Jobs

Submit short jobs without requeue (the default). You control when and where to resubmit preempted jobs.

With `--requeue`, jobs restart automatically but may get preempted repeatedly.

!!! note
    There is a cap of 1000 jobs per user per cluster on the scavenger partition.

### Long Restartable Jobs

For longer jobs that checkpoint their progress:

- Create intermediate outputs so work is not lost on preemption
- Use `--requeue` to have Slurm automatically restart cancelled jobs
- Ensure your code detects checkpoint files and resumes from them

## Submitting to Scavenger

```bash
#SBATCH --partition=scavenger
```

For GPU scavenger, also specify the node type:

```bash
#SBATCH --constraint=<partition>
```

Where `<partition>` is one of: `gtx1080`, `titanx`, `k40`, `v100`.

## Preemption Behavior

By default, preempted jobs are **cancelled**. To have them requeued automatically:

```bash
#SBATCH --requeue
```

!!! warning
    It is possible for paid-partition jobs to have lower priority than scavenger jobs in rare cases. If you observe this, [submit a ticket](https://crc.pitt.edu/tickets) with the output of:
    ```
    sprio -M gpu -S "-y" --format %.15i,%.9r,%.10Y,%.10u
    squeue -M gpu
    ```
