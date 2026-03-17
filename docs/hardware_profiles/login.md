---
tags:
  - CRC
  - Hardware
  - Login Nodes
  - User Manual
---

# Login Nodes

Login nodes serve as the primary entrypoint for accessing CRCD systems.They provide secure command-line access over SSH,
allowing users to submit jobs, manage files, and launch interactive tasks.

Login nodes are a shared resource that provide fast network access and responsive storage for tasks such as preparing
data, compiling code, or submitting jobs. **They are not intended for running large-scale analysis or compute-heavy
tasks.** Running intensive computations directly on login nodes can slow down systems for all users, and built-in limits
on CPU, memory, and runtime may prevent larger jobs from completing successfully. For analysis and compute-heavy tasks,
users should submit jobs through Slurm to the compute nodes, which are designed to handle high-performance workloads.

## Per-User Resource Limits

To protect the shared login environment, per-user resource limits are enforced via cgroups:

| Resource | Per-User Limit |
|----------|---------------|
| CPU cores | 1 |
| Memory    | 8 GB |

These limits are **collective** — they apply to the combined usage of all processes you are running on the login node, regardless of how many there are. When the login node's overall resource pool is exhausted, users may be unable to log in until utilization falls below the threshold.

Processes that exceed these limits or that run compute-intensive workloads may be terminated without notice.

If you need to keep a lightweight process running after disconnecting (e.g., monitoring a job submission or editing files), use [tmux or nohup](../tips/persist-sessions.md) rather than leaving long-running compute work on the login node. For any resource-intensive work, request a [dedicated interactive compute node](../slurm/interactive-jobs.md) or submit a [batch job](../slurm/batch-jobs.md).

## Specifications

| hostname          | backend hostname    | Architecture                                                                                                                                                         | Cores/Node | Mem    | Mem/Core | OS Drive                | Network |
|-------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------|--------|----------|-------------------------|---------|
| h2p.crc.pitt.edu  | login0.crc.pitt.edu | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32         | 256 GB | 8 GB     | 2x 480 GB NVMe (RAID 1) | 25GbE   |
|                   | login1.crc.pitt.edu | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32         | 256 GB | 8 GB     | 2x 480 GB NVMe (RAID 1) | 25GbE   |
| htc.crc.pitt.edu  | login3.crc.pitt.edu | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32         | 256 GB | 8 GB     | 2x 480 GB NVMe (RAID 1) | 25GbE   |
