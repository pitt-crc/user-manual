# Slurm Batch Jobs

For your *first* batch job, start with
[**Requesting Resources**](../getting-started/step3/getting-started-step3-resources.md)
and [**Managing Jobs**](../getting-started/step3/getting-started-step3-manage-jobs.md)
in Getting Started, which walk through a simple example end to end. This page is
the fuller reference: the directives you can use, a complete submission script,
and answers to common questions.

!!! tip "Test interactively first"
    Before scaling up to a batch job, it's often worth running a smaller version
    in an [Interactive Job](interactive-jobs.md) to make sure it works.

## Common `#SBATCH` directives

These are the arguments you'll use most often. See the [Slurm `sbatch` documentation](https://slurm.schedmd.com/sbatch.html)
for the complete list. The Slurm directives following the syntax `#SBATCH <argument>=<value>`, where `<argument>` is one
of the defined parameters below and `<value>` is the desired resource setting in the appropriate format.

| Argument | Description | Format / example |
| -------- | ----------- | ---------------- |
| `--job-name` | Name shown in `squeue`. | Something descriptive; defaults to the Job ID |
| `--cluster` | Cluster to run on. | `smp`, `mpi`, `gpu`, `htc` |
| `--partition` | Partition within the cluster. | See [Hardware Profiles](../hardware_profiles/index.md) |
| `--constraint` | Target a specific hardware type | See [Hardware Profiles](../hardware_profiles/index.md) for options| 
| `--nodes` | Number of nodes. | Usually `1`; MPI needs ≥ 2. Default `1` |
| `--ntasks-per-node` | Tasks (processes) launched per node. | Default `1` |
| `--cpus-per-task` | CPUs per task, for multithreading. | e.g. `16` |
| `--mem` | Memory per node. | e.g. `16G` (or MB, e.g. `16000`) |
| `--gres` | Generic resources; on GPU jobs, the card count. | `gpu:1`. **Required** on the GPU cluster |
| `--time` | Maximum walltime. | `days-HH:MM:SS` |
| `--qos` | Quality of Service (caps walltime, affects priority). | Default `normal`; see below |
| `--output` | File for standard output. | e.g. `myjob_%j.out` (`%j` = Job ID) |
| `--error` | File for standard error (if separate from output). | full path or filename |
| `--account` | Charge a specific allocation. | Resource Allocation name (see [FAQ](#faq)) |
| `--mail-user` | Email address for notifications. | `PittID@pitt.edu` |
| `--mail-type` | When to notify. | `END`, `FAIL` (comma-separated) |

!!! info "QoS levels and limits live in one place"
    Rather than list QoS walltimes and limits here (they change), see the
    authoritative tables on the
    [**Job Limits & QoS**](job-limits.md)
    page. The default is `normal`; request another with `--qos=<name>`.

## A job submission script template

The template below touches all key elements of the script: defining the hardware resources, loading modules, 
staging inputs on fast scratch storage, running the program, and copying results back.

```bash
#!/bin/bash
#SBATCH --job-name=<job_name>
#SBATCH --cluster=<cluster name>
#SBATCH --partition=<partition>
#SBATCH --nodes=<number of nodes>
#SBATCH --ntasks-per-node=<tasks per node>
#SBATCH --time=<days-HH:MM:SS>
#SBATCH --mail-user=<PittID>@pitt.edu
#SBATCH --mail-type=END,FAIL
#SBATCH --qos=<qos>

module purge
module load module1 module2

cp <inputs> $SLURM_SCRATCH
cd $SLURM_SCRATCH
run_on_exit(){ cp -r $SLURM_SCRATCH/* $SLURM_SUBMIT_DIR; }
trap run_on_exit EXIT

srun <job executable with parameters>

crc-job-stats

cp <outputs> $SLURM_SUBMIT_DIR
```
### Anatomy of the script

**Specify the interpreter.** The shebang (`#!`) line tells the OS which tool to use
to process the script. The tool can be a shell command or scripting language available 
on the cluster, e.g. `#!/bin/bash`, `#!/bin/tcsh`, `#!/usr/bin/env python3`. 

**Slurm Directives.** The `#SBATCH` lines that follow are directives to Slurm, instructing
it what resources to provision for execution of the job script.

**Load modules.** Declare the software your job needs. A `module purge` first
removes any previously loaded software and starts with a clean environment. See
[Discovering Software](../getting-started/step3/getting-started-step3-software.md)
and the [Application Environment](../applications/application-environment.md).

**Handle inputs.** Your job's working directory defaults to where you submitted
from; the example stages inputs to scratch instead. Adjust the working directory
with [`--chdir`](https://slurm.schedmd.com/sbatch.html#OPT_chdir) if you prefer.

**Run with `srun`.** `srun` launches your program. It accepts `--nodes`,
`--ntasks-per-node`, and `--cpus-per-task` to vary resources per step, but never
above what `sbatch` was given.

**Report statistics.** The `crc-job-stats` command appends a summary of the
resources your job used to its output, so that you can right-sizing future jobs more accurately.

**Handle outputs.** Copy results back and do any post-processing at the end.

**After submission.** Monitoring, inspecting, and cancelling jobs (`squeue`,
`scontrol`, `scancel`) are covered end to end in
[**Managing Jobs**](../getting-started/step3/getting-started-step3-manage-jobs.md).

!!! note "Why copy to `$SLURM_SCRATCH`?"
    Staging data on node-local scratch speeds up I/O-heavy jobs and keeps load
    off the shared filesystems. The `trap` ensures results are copied back even
    if the job exits early. See
    [**Utilizing Scratch Space**](scratch-storage.md) for the full explanation.

## GPU jobs

A GPU job uses the same template with the cluster and partition values changed 
accordingly and the number of GPUs requested via `--gres` parameter:

```bash
#!/bin/bash
#SBATCH --job-name=<job_name>
#SBATCH --cluster=gpu
#SBATCH --partition=l40s          # a100 | a100_multi | a100_nvlink | l40s | h200 | rtx6k
#SBATCH --nodes=1
#SBATCH --gres=gpu:<number of GPUs wanted>
#SBATCH --time=<days-HH:MM:SS>

<commands to run your GPU code>
```

See the [GPU cluster](../hardware_profiles/gpu.md) hardware page for the current
partitions and what each provides.

## Frequently Asked Questions { #faq }

**1. I supplied `--mail-user` and `--mail-type` but get no email. Why?**

Most often the provided email address is missing the domain. It must be `<PittID>@pitt.edu`, not
just your username. On rare occasions, the email queue gets backed up from one user
submitting many jobs. This backlog typically clears up on its own, but when submitting large batches
it's good etiquette to comment out the email directive from the jobs and monitor with `squeue` instead.

**2. Where can I find more example Slurm batch scripts?**

Example jobs using commonly loaded modules are in `/ihome/crc/how_to_run`. For
NGS analyses on HTC, see the [RNASeq notes](../advanced-genomics-support/RNASeq-data-analysis.md).

**3. How do `--nodes`, `--ntasks`, and `--cpus-per-task` interact?**

A **node** is a physical compute node. A **task** is essentially a process, tied
to the CPUs/cores you request. Common cases:

- **Independent processes on one node** — `--ntasks=16` implies `--nodes=1`,
  `--ntasks-per-node=16`.
- **MPI across nodes** — `--ntasks=16` alone lets Slurm spread 16 processes
  however it likes; use `--ntasks-per-node` to control the layout.
- **One multithreaded process** — `--ntasks=1 --cpus-per-task=16`.

On HTC, SMP, and GPU a single task can't span nodes, so `--cpus-per-task` always
lands all its cores on one node.

**4. Slurm isn't picking up my `~/.bashrc` changes. Why?**

Slurm doesn't source `~/.bashrc` or `~/.profile`. If your job needs settings
from them, add `source ~/.bashrc` after your `module load` commands.

**5. Which allocation is my job charging from, and how do I change it?**

Check with 

```bash
sacctmgr show associations onlydefaults format=cluster,account%30s,user | grep $USER
```
The output lists the default Resource Allocation charged for each cluster usage.

!!! note
    If you belong to multiple Resource Allocations, run the command without
    `onlydefaults` to list them all. Then charge to a specific one with the directive:

    ```bash
    #SBATCH --account=<group>    # Charge <group> instead of the default Resource Allocation
    ```

**6. I need to run the same job over many inputs. Is there a better way?**

Yes. See [**Job Arrays**](job-arrays.md).

## Where to go next

<div class="grid cards" markdown>

-   :material-clipboard-check:{ .lg .middle } __Manage running jobs__

    ---

    Submit, monitor, inspect, and cancel jobs.

    [:octicons-arrow-right-24: Managing Jobs](../getting-started/step3/getting-started-step3-manage-jobs.md)

-   :material-file-multiple-outline:{ .lg .middle } __Submit many at once__

    ---

    Run the same script across many inputs with a job array.

    [:octicons-arrow-right-24: Job Arrays](job-arrays.md)

-   :material-currency-usd:{ .lg .middle } __Understand the cost__

    ---

    How Service Units are calculated and charged.

    [:octicons-arrow-right-24: Service Units](service-units.md)

</div>
