# Job Scheduling Policy

## Login Node Usage Policy

Login nodes are shared gateways for lightweight interactive work: editing code, submitting jobs, and checking status.

**Per-user resource limits (enforced via cgroups):**

| Resource | Limit |
|----------|-------|
| CPU cores | 1 |
| Memory | 8 GB |

These limits apply collectively across all processes a user is running. Resource-intensive processes may be terminated without notice.

!!! warning
    Repeated abuse of login nodes may result in revoked cluster access. Use [interactive jobs](../slurm/interactive-jobs.md) or [batch jobs](../slurm/batch-jobs.md) for computational work.

![JOB-SCHEDULING-POLICY1](../_assets/img/policies/job_scheduling_policy_1.png)

## Job Priority

Slurm uses a multi-factor priority system called "Fair Share" to ensure all groups have equitable access. Priority is computed as a weighted sum of four factors:

| Factor | Range | Description | Weight |
|--------|-------|-------------|--------|
| Age | [0, 1] | Time spent in queue (max at 7 days) | 2000 |
| Job Size | [0, 1] | Resources requested relative to cluster capacity | 2000 |
| QoS | [0, 1] | Based on walltime tier (shorter = higher) | 2000 |
| Fair Share | [0, 1] | Overall allocation usage (less usage = higher) | 3000 |

### Quality of Service (QoS) Tiers

| QoS | Priority | Factor | Max Walltime |
|-----|----------|--------|-------------|
| Short | 13 | 1.00 | 1-00:00:00 |
| Normal | 12 | 0.92 | 3-00:00:00 |
| Long | 11 | 0.84 | 6-00:00:00 |
| Long-long (SMP/HTC only) | 10 | 0.76 | 21-00:00:00 |

### Checking Job Priority

```commandline
sprio -M smp -u $USER -j <jobid>
```

## Walltime Extensions

Walltime extensions are generally not granted. Users should benchmark their jobs and specify appropriate time requirements. This ensures proper Fair Share queueing and timely resource availability for other users.

## Pending Job Reasons

### Resource Availability

| Reason | Explanation | Resolution |
|--------|-------------|------------|
| Resources | Cluster is fully utilized | Wait for resources to free up |
| Priority | Other jobs have higher priority | Wait for priority to increase |
| Dependency | Waiting for another job to finish | Wait for dependency to complete |
| DependencyNeverSatisfied | Dependent job failed | Cancel the job and fix the dependency |

### Usage Limits

#### JobArrayTaskLimit / QOSMaxJobsPerUserLimit / QOSMaxJobsPerAccountLimit

Your jobs have exceeded the limit on actively priority-accruing jobs:

- **Per-account limit**: 500 jobs
- **Per-user limit**: 100 jobs
- **Max job array size**: 500 (SMP/MPI/HTC), 1001 (GPU)
- **Max submitted jobs**: 1000 total

Check limits with:
```commandline
sacctmgr show qos format=Name%20,MaxJobsPA,MaxJobsPU,MaxSubmitJobsPA,MaxSubmitJobsPU,MaxTresPA%20
```

#### MaxMemoryPerAccount

Your group has exceeded its aggregate memory quota. Maximum memory (GB) per QoS:

| Cluster | Partition | Short | Normal | Long |
|---------|-----------|-------|--------|------|
| SMP | smp | 13,247 | 11,044 | 9,999 |
| SMP | high-mem | 6,512 | 6,512 | 6,512 |

Use `crc-seff -M <cluster> <jobid>` to check your jobs' memory efficiency and reduce requests if possible.

#### AssocGrpBillingMinutes

Your allocation's service units are exhausted or expired. Check with `crc-usage` and [request a new allocation](https://crc.pitt.edu/service-request-forms/compute-allocation-guidelines) if needed.

#### MaxTRESPerAccount / MaxCpuPerAccount / MaxGRESPerAccount

Your group has exceeded its concurrent CPU/GPU limit. Limits per QoS tier:

| Cluster | Partition | Short (1 day) | Normal (3 days) | Long (6 days) | Long-long (21 days) |
|---------|-----------|---------------|-----------------|---------------|---------------------|
| SMP | smp | 2,304 | 1,613 | 1,152 | 461 |
| SMP | high-mem | 320 | 224 | 160 | - |
| GPU | a100 | 16 | 12 | 8 | - |
| GPU | a100_multi | 32 | 24 | 8 | - |
| GPU | a100_nvlink | 24 | 16 | 8 | - |
| GPU | l40s | 24 | 16 | 8 | - |
| MPI | mpi | 3,264 | 2,285 | 1,632 | - |
| HTC | htc | 1,536 | 1,075 | 768 | 307 |
| Teach | cpu | 1,152 | 1,152 | 1,152 | 1,152 |
| Teach | gpu | 24 | 24 | 24 | 24 |
