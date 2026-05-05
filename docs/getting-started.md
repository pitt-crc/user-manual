# Getting Started at CRCD

This guide walks you through the essential steps to begin using CRCD computing resources at the University of Pittsburgh.

## Step 1: Get an Account

To access CRCD resources, you need an active account:

1. **Faculty/PIs**: Request an allocation at [crc.pitt.edu](https://crc.pitt.edu/getting-started)
2. **Students/Staff**: Your PI must add you to their allocation
3. **External Collaborators**: Request a [Sponsored Account](getting-started/sponsored_account.md)

!!! note "Prerequisites"
    - Active University of Pittsburgh credentials
    - [Pitt VPN](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=293) installed (required for off-campus access)
    - Duo multi-factor authentication configured

## Step 2: Connect to the Cluster

Choose your preferred access method:

| Method | Description | Best For |
|--------|-------------|----------|
| [SSH Terminal](web-portals/terminal.md) | Command-line access via SSH | Experienced Linux users |
| [Open OnDemand](web-portals/open-ondemand.md) | Web-based portal | Job management, file browsing |
| [JupyterHub](web-portals/jupyter-hub.md) | Interactive notebooks | Data analysis, prototyping |
| [VIZ Desktop](web-portals/viz.md) | Remote Linux desktop | GUI applications, visualization |

For detailed connection instructions, see [Connecting to CRCD](connect/index.md).

## Step 3: Understand the Environment

### Software Modules

CRCD uses [Lmod](applications/application-environment.md) to manage software environments:

```bash
# Search for software
module spider python

# Load a module
module load python/3.11.2

# See what's loaded
module list
```

### Storage

Your account includes access to multiple file systems:

| Path | Quota | Purpose | Backed Up |
|------|-------|---------|-----------|
| `/ihome/<username>` | 75 GB | Home directory, scripts, configs | Yes |
| `/bgfs/<PI>/<username>` | 5 TB (shared) | Research data, project files | No |
| `/ix1/<PI>/<username>` | 5 TB (shared) | Research data, project files | No |

For details, see [File Systems](data-management/file-systems.md).

### Submitting Jobs

All compute work runs through the [Slurm job scheduler](jobs/index.md):

```bash
# Submit a batch job
sbatch my_job.slurm

# Check your jobs
squeue -u $USER

# Start an interactive session
srun -n 1 --cpus-per-task=4 --mem=8G --time=01:00:00 --pty bash
```

For a complete guide to running jobs, see [Running Jobs](jobs/index.md).

## Step 4: Get Help

- Browse the [FAQ](faq.md) for common issues
- Submit a ticket at the [help portal](https://services.pitt.edu/TDClient/33/Portal/Requests/ServiceDet?ID=1511)
- Check [CRCD office hours](https://crc.pitt.edu) for in-person assistance

## Top Documentation Pages

* [Running Jobs](jobs/index.md) - Slurm commands, job scripts, submitting jobs
* [Working with Software](applications/index.md) - Modules, Python, R, and more
* [Managing Data](data-management/index.md) - File systems, transfers, permissions
* [Understanding Our Systems](systems/index.md) - Cluster specifications and selection guide
* [Connecting to CRCD](connect/index.md) - All access methods and portals
