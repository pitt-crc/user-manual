# Requesting Computing Resources

Because CRCD operates a shared resource for the Pitt research community, there
needs to be a tool that ensures fair and equitable access. CRCD uses the
[**Slurm workload manager**](https://slurm.schedmd.com/quickstart.html) to
accomplish this. Slurm is a batch queueing system that allocates resources based
on defined policies, and it supports two ways of working:

!!! tip "Interactive or batch — which do I use?"
    - **Interactive** — you get a compute node and type commands live. Best for
      testing, exploring data, and debugging.
    - **Batch** — you submit a script and Slurm runs it unattended when resources
      are available. Best for production and long-running work.

    Throughout the examples, `<variable>` marks a placeholder you replace with
    your own value.

## What you're requesting

Whether interactive or batch, you're asking Slurm for the same things: a
**cluster**, optionally a **partition** within it, some number of **cores** and
**nodes**, an amount of **memory**, a **wall-time** limit, and optionally
**GPUs**. With the `crc-interactive` command, those map to the following flags:

| To set… | Flag | Default |
| ------- | ---- | ------- |
| Cluster | `-s` smp · `-g` gpu · `-m` mpi · `-d` htc · `-e` teach | — |
| Partition | `-p <name>` | cluster dependent |
| Cores per node | `-c <n>` | 1 |
| Nodes | `-n <n>` | 1 |
| Memory (GB) | `-b <GB>` | partition dependent |
| Wall time | `-t <hours or HH:MM:SS>` | 01:00:00 |
| GPUs (with `-g`) | `-u <n>` | 0 |
| Account / allocation | `-a <group>` | your default allocation |

Not sure which cluster or partition to pick? See the
[**Hardware Profiles**](../../hardware_profiles/overview.md) section. The same
concepts map to `#SBATCH` directives in a batch script — those are shown in the
[**batch section**](#batch-processing) below.

!!! note "What is the cost for my job?"
    Jobs debit [**Service Units**](../../slurm/service-units.md) from your
    research group's Resource Allocation. If you belong to more than one Resource Allocation,
    you can designate which to charge with the `-a <group>` (interactive) or `#SBATCH --account=<group>`
    (batch) options.

## Interactive work

Request interactive resources through the
[**Open OnDemand web portal**](../open-ondemand.md) or from the terminal with the
`crc-interactive` command. The simplest possible request grabs the defaults —
one core on one SMP node for one hour:

```bash
crc-interactive -s
```

When the session starts, your shell prompt changes from a login node to a compute
node — that's how you know you're no longer on the shared gateway.

To request more resources, add the corresponding flags and desired values. For example, to request **8 
cores and 256 GB of RAM on the high-mem partition of the SMP cluster for 12 hours**:

=== "command"

    ```bash
    crc-interactive -s -p high-mem -c 8 -b 256 -t 12:00:00
    ```

=== "output"

    ```
    [kimwong@login1.crc.pitt.edu ~]$crc-interactive -s -p high-mem -c 8 -b 256 -t 12:00:00
    srun: job 16615902 queued and waiting for resources
    srun: job 16615902 has been allocated resources
    [kimwong@smp-1024-n8.crc.pitt.edu ~]$
    ```

!!! note
    See how the hostname in the output changes from `login1.crc.pitt.edu` to
    `smp-1024-n8.crc.pitt.edu` once Slurm allocates the requested resources.

!!! tip "Learn the underlying Slurm command"
    Add `-z` to any `crc-interactive` command to print the equivalent `srun`
    command without running it — a good way to learn the raw Slurm syntax.

??? note "Full `crc-interactive` options"
    ```
    usage: crc-interactive [-h] [-v] [-z] [-s] [-g] [-m] [-i] [-d] [-e] [-p PARTITION] [-b MEM] [-t TIME] [-n NUM_NODES] [-c NUM_CORES] [-u NUM_GPUS] [-a ACCOUNT] [-r RESERVATION] [-l LICENSE]
                           [-f FEATURE] [-o]

    Launch an interactive Slurm session.

    optional arguments:
      -h, --help                                 show this help message and exit
      -v, --version                              show program's version number and exit
      -z, --print-command                        print the equivalent slurm command and exit

    Cluster Arguments:
      -s, --smp                                  launch a session on the smp cluster
      -g, --gpu                                  launch a session on the gpu cluster
      -m, --mpi                                  launch a session on the mpi cluster
      -i, --invest                               launch a session on the invest cluster
      -d, --htc                                  launch a session on the htc cluster
      -e, --teach                                launch a session on the teach cluster
      -p PARTITION, --partition PARTITION        run the session on a specific partition

    Arguments for Increased Resources:
      -b MEM, --mem MEM                          memory in GB
      -t TIME, --time TIME                       run time in hours or hours:minutes [default: 01:00:00]
      -n NUM_NODES, --num-nodes NUM_NODES        number of nodes [default: 1]
      -c NUM_CORES, --num-cores NUM_CORES        number of cores per node [default: 1]
      -u NUM_GPUS, --num-gpus NUM_GPUS           if using -g, the number of GPUs [default: 0]

    Additional Job Settings:
      -a ACCOUNT, --account ACCOUNT              specify a non-default account
      -r RESERVATION, --reservation RESERVATION  specify a reservation name
      -l LICENSE, --license LICENSE              specify a license
      -f FEATURE, --feature FEATURE              specify a feature, e.g. `ti` for GPUs
      -o, --openmp                               run using OpenMP style submission
    ```

## Batch processing

Batch processing requires a script, which you submit to Slurm with
`sbatch <job_submission_script>`. A submission script has three parts: a section
of **Slurm directives** requesting resources, a section that **loads the software
environment**, and a section that **runs the software**.

The example below runs [**Amber molecular dynamics**](https://ambermd.org/) on a
GPU. Use the tabs to see the whole script, then each part explained.

=== "Whole script"

    ```bash
    #!/usr/bin/env bash

    ## ------------------------------------------------------------------
    ## Slurm directives defining the resource request
    ## ------------------------------------------------------------------
    #SBATCH --job-name=gpus-1
    #SBATCH --output=gpus-1.out
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --gres=gpu:1
    #SBATCH --time=24:00:00

    ## ---------------------------------------------------------------------
    ## Load software into environment
    ## ---------------------------------------------------------------------
    module purge
    module load gcc/10.2.0  openmpi/4.1.1
    module load amber/24

    ## ---------------------------------------------------------------------
    ## Setup software execution environment
    ## ---------------------------------------------------------------------
    # Define environmental variables for Amber input/output files
    INP=md.in
    TOP=mocvnhlysm.top
    CRD=mocvnhlysm.crd
    OUT=mocvnhlysm

    # Define software executable
    SANDER=pmemd.cuda

    # Display environmental variables to Slurm output file for diagnostics
    echo AMBERHOME    $AMBERHOME
    echo SLURM_NTASKS $SLURM_NTASKS
    echo which SANDER `which $SANDER`
    echo "Running on node:" `hostname`

    # Display NVIDIA GPU information to Slurm output file
    nvidia-smi

    # Software execution line
    $SANDER  -O     -i   $INP   -p   $TOP   -c   $CRD   -r   $OUT.rst \
                    -o   $OUT.out   -e   $OUT.ene   -v   $OUT.vel   -inf $OUT.nfo   -x   $OUT.mdcrd
    ```

=== "Part 1: Slurm directives"

    ```bash
    ## ------------------------------------------------------------------
    ## Slurm directives defining the resource request
    ## ------------------------------------------------------------------
    #SBATCH --job-name=gpus-1
    #SBATCH --output=gpus-1.out
    #SBATCH --nodes=1
    #SBATCH --ntasks-per-node=1
    #SBATCH --cluster=gpu
    #SBATCH --partition=l40s
    #SBATCH --gres=gpu:1
    #SBATCH --time=24:00:00
    ```

    A Slurm directive begins with `#SBATCH` followed by `--<variable>=<value>`,
    where `<variable>` is one of the options defined for the
    [**sbatch**](https://slurm.schedmd.com/sbatch.html) command. The specific
    `<value>` is unique to how CRCD configured Slurm and is documented in the
    [**Slurm Batch Jobs**](../../slurm/batch-jobs.md) section.

    !!! info "Which lines are GPU-specific?"
        `--cluster=gpu`, `--partition=l40s`, and `--gres=gpu:1` request a GPU. A
        CPU-only job drops the `--gres` line and targets a CPU cluster instead,
        e.g. `--cluster=smp --partition=smp`. See
        [**Slurm Batch Jobs**](../../slurm/batch-jobs.md) for a generic template
        and [**Basic Slurm Commands**](../../applications/basic-commands.md) for
        the difference between `--nodes`, `--ntasks-per-node`, and
        `--cpus-per-task`.

    !!! info "Why do some `#` lines run and others don't?"
        Most Linux shells treat a line starting with `#` as a comment. Slurm,
        however, reads every `#SBATCH` line as a directive to it. Any other line
        starting with `#` (such as the `##` separators above) is just a comment.

=== "Part 2: Load software"

    ```bash
    ## ---------------------------------------------------------------------
    ## Load software into environment
    ## ---------------------------------------------------------------------
    module purge
    module load gcc/10.2.0  openmpi/4.1.1
    module load amber/24
    ```

    Purge everything first, then load only what you need. Loading a module
    updates `$PATH`, `$LD_LIBRARY_PATH`, and other variables so the software is
    available. See [**Discovering Software**](getting-started-step3-software.md)
    for how to find modules and their dependencies.

=== "Part 3: Run software"

    ```bash
    ## ---------------------------------------------------------------------
    ## Setup software execution environment
    ## ---------------------------------------------------------------------
    # Define environmental variables for Amber input/output files
    INP=md.in
    TOP=mocvnhlysm.top
    CRD=mocvnhlysm.crd
    OUT=mocvnhlysm

    # Define software executable
    SANDER=pmemd.cuda

    # Display environmental variables to Slurm output file for diagnostics
    echo AMBERHOME    $AMBERHOME
    echo SLURM_NTASKS $SLURM_NTASKS
    echo which SANDER `which $SANDER`
    echo "Running on node:" `hostname`

    # Display NVIDIA GPU information to Slurm output file
    nvidia-smi

    # Software execution line
    $SANDER  -O     -i   $INP   -p   $TOP   -c   $CRD   -r   $OUT.rst \
                    -o   $OUT.out   -e   $OUT.ene   -v   $OUT.vel   -inf $OUT.nfo   -x   $OUT.mdcrd
    ```

    This section contains the software-specific setup and execution lines.
    Typically you can copy the commands you'd run on your own laptop or desktop
    with only minor changes, translating the software's documented commands to
    work within the CRCD ecosystem.

## Next steps

You've requested resources — the next stage is submitting and monitoring your
work. Continue to **[Managing Jobs](getting-started-step3-manage-jobs.md)**.

<div class="grid cards" markdown>

-   :material-file-document-edit:{ .lg .middle } __Write fuller job scripts__

    ---

    Common `#SBATCH` directives, CPU and GPU templates, and email notifications.

    [:octicons-arrow-right-24: Slurm Batch Jobs](../../slurm/batch-jobs.md)

-   :material-console:{ .lg .middle } __More on interactive jobs__

    ---

    Using `srun`, choosing an account, and interactive sessions on any cluster.

    [:octicons-arrow-right-24: Interactive Jobs](../../slurm/interactive-jobs.md)

-   :material-currency-usd:{ .lg .middle } __Understand the cost__

    ---

    How Service Units are calculated and charged against your allocation.

    [:octicons-arrow-right-24: Service Units](../../slurm/service-units.md)

</div>
