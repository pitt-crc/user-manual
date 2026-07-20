# Quick Start: Your First Job

This page takes a brand-new user from a fresh account to a completed job in a
handful of copy-paste steps. It is deliberately short and opinionated. Once a
step makes sense, follow the linked reference pages for the full detail.

!!! note "Before you begin"
    You need two things first:

    1. A **CRCD account and resource allocation** — see [Step 1: Getting an Account](getting-started-step1-account.md).
    2. An active **PittNet VPN (GlobalProtect)** connection — the clusters are firewalled within PittNet, so nothing below will work without it.

    Your username is your **Pitt username in all lowercase**.

---

## 1. Log in

The fastest way in is SSH from a terminal. Connect to a login node with:

```bash
ssh -X <pittID>@h2p.crc.pitt.edu
```

`h2p.crc.pitt.edu` reaches the SMP, MPI, and GPU clusters; use
`htc.crc.pitt.edu` for the HTC cluster. The `-X` flag forwards graphics so
GUI applications can display on your machine.

Prefer a browser instead of a terminal? Open
[https://ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu) and sign
in with the same credentials — see [Step 2](step2/index.md) for a comparison of every
access portal and when to use each.

!!! warning "Don't compute on the login nodes"
    Login nodes are shared gateways for editing files and submitting jobs — **not** for
    running your analysis. Heavy processes there slow everyone down and may be killed.
    Always run real work in an interactive session or a batch job (below).

---

## 2. Find and load software

CRCD provides software through [Lmod](https://github.com/TACC/Lmod) environment
modules rather than system-wide installs. Search for what you need, then load it
into your environment for the current session.

=== "Search"

    ```bash
    module spider python
    ```

=== "Load"

    ```bash
    # Load dependencies first, then the package (order matters):
    module load gcc/8.2.0 python/anaconda3.10-2022.10
    ```

=== "Check / clear"

    ```bash
    module list     # what's loaded now
    module purge    # unload everything
    ```

`module spider <name>` lists every available version and tells you which
dependencies to load first. Full details and more examples are on
[Discovering Software](step3/getting-started-step3-software.md).

---

## 3a. Run interactively (for testing)

To grab a compute node for hands-on work — testing a script, exploring data —
use the `crc-interactive` helper, which requests a Slurm session for you:

```bash
crc-interactive --help    # see all options (cluster, cores, memory, time, GPUs)
```

This drops you onto a real compute node where it is safe to run your code. See
[Requesting Resources](step3/getting-started-step3-resources.md) and
[Interactive Jobs](../slurm/interactive-jobs.md) for the full option list.

---

## 3b. Submit a batch job (for real work)

Most work runs unattended as a **batch job**. Create a file called
`first-job.slurm`:

```bash
#!/bin/bash
#SBATCH --job-name=first-job
#SBATCH --cluster=smp          # smp | htc | mpi | gpu
#SBATCH --partition=smp        # confirm partition names on the Requesting Resources page
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=00:05:00
#SBATCH --output=first-job_%j.out
# #SBATCH --account=<your_group>   # uncomment if your group requires it

echo "Hello from $(hostname)"
date
module list
```

Submit it:

```bash
sbatch first-job.slurm
```

Slurm replies with a job ID, e.g. `Submitted batch job 956929 on cluster smp`.
When the job finishes, your output waits in `first-job_<jobid>.out`.

!!! tip "Choosing a cluster"
    Match the cluster to your workload. Details on each are in the
    [Hardware Profiles](../hardware_profiles/overview.md) section.

    | Cluster | Best for |
    | ------- | -------- |
    | **SMP** | Single-node jobs sharing one memory space |
    | **HTC** | Genomics / health-sciences, single-node throughput |
    | **MPI** | Tightly coupled multi-node parallel (MPI) codes |
    | **GPU** | AI/ML and GPU-accelerated simulation |

---

## 4. Check on your job

```bash
squeue -M <cluster> -u $USER      # status of your jobs on one cluster
squeue -M all -u $USER            # ...across every cluster
scontrol -M <cluster> show job <jobid>   # full detail for one job
```

`<cluster>` is any of `smp`, `htc`, `mpi`, `gpu` (or a comma-separated list).
More on monitoring, holding, and cancelling jobs is on
[Managing Jobs](step3/getting-started-step3-manage-jobs.md).

---

## You're up and running

You have logged in, loaded software, and completed a job. Sensible next stops:

- [Slurm Batch Jobs](../slurm/batch-jobs.md) — write more capable job scripts
- [Service Units](../slurm/service-units.md) — understand how usage is charged
- [File Systems](../data-management/file-systems.md) — where to keep your data
- [Frequently Asked Questions](../faq.md) — quick answers to common snags

!!! question "Stuck?"
    Open a ticket or reach the CRCD team via the
    [service catalog](https://crc.pitt.edu/service-request-forms).
