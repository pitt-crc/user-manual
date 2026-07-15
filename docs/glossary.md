# Glossary

New to high-performance computing? This page explains the terms you'll meet
most often across the manual. Terms are grouped by theme; acronyms also appear
as hover tooltips throughout the rest of the site.

## The basics

CRCD Ecosystem
: The complement of CRCD infrastructure, including hardware, software, domain experts and services

Resource Allocation
: An allotment of computing time and/or data storage quota

HPC (High-Performance Computing)
:   Using many connected computers together to run computational work that is
    too large or slow for a single desktop or laptop.

Cluster
:   A large collection of connected computers ("nodes") that work as one system.
    CRCD runs several clusters tuned for different workloads — see
    [Hardware Profiles](hardware_profiles/overview.md).

Node
:   A single computer within a cluster. Nodes come in two kinds below: login
    nodes and compute nodes.

Login node
:   The gateway machine you land on when you connect. Use it to edit files,
    manage data, and submit jobs — **not** to run heavy computation. See
    [Login Nodes](hardware_profiles/login.md).

Compute node
:   A worker machine where your jobs actually run. You don't log into these
    directly; the scheduler assigns them to your job.

## Hardware

Core (CPU core)
:   A single processing unit within a node. A job asking for "16 cores" reserves
    16 of a node's cores to run in parallel.

Thread
:   A stream of execution within a program. Many programs run one thread per
    core; some run several.

GPU (Graphics Processing Unit)
:   A specialized processor that accelerates AI/ML and certain simulations. See
    the [GPU cluster](hardware_profiles/gpu.md).

RAM (memory)
:   The fast working memory a job uses while running, requested separately from
    cores. Running out of memory is a common cause of failed jobs.

## The CRCD clusters

SMP cluster
:   **Symmetric Multi-Processing** — for jobs that run on a single node and share
    one pool of memory. See [SMP](hardware_profiles/smp.md).

MPI cluster
:   For tightly coupled jobs that span **multiple** nodes using the Message
    Passing Interface. See [MPI](hardware_profiles/mpi.md).

HTC cluster
:   **High-Throughput Computing** — for many independent single-node jobs, widely
    used for genomics and health-sciences work. See [HTC](hardware_profiles/htc.md).

GPU cluster
:   Nodes equipped with GPUs for accelerated computing. See [GPU](hardware_profiles/gpu.md).

TEACH cluster
:   Resources set aside for courses and teaching. See [TEACH](hardware_profiles/teach.md).

VIZ
:   A graphical Linux desktop environment for visualization and GUI applications.
    See [VIZ](hardware_profiles/viz.md).

## Jobs and scheduling

Job
:   A unit of work you submit to a cluster — a script plus the resources it needs
    (cores, memory, time).

Batch job
:   A job that runs unattended: you submit a script and the scheduler runs it
    when resources are free. Most work runs this way. See
    [Slurm Batch Jobs](slurm/batch-jobs.md).

Interactive job
:   A session on a compute node where you type commands live, useful for testing
    and exploration. See [Interactive Jobs](slurm/interactive-jobs.md).

Scheduler
:   The software that decides which jobs run where and when, balancing everyone's
    requests fairly.

Slurm
:   The specific scheduler ("workload manager") CRCD uses. You interact with it
    through commands like `sbatch`, `squeue`, and `scontrol`. See the
    [Slurm Overview](slurm/slurm-overview.md).

Partition
:   A named subset of a cluster's nodes that jobs are submitted to, often grouped
    by hardware type or purpose.

Scavenger partition
:   A low-priority partition that runs on idle resources at no service-unit cost,
    but whose jobs can be **preempted** (interrupted) when higher-priority work
    needs the nodes. See [Scavenger Partitions](slurm/scavenger.md).

Walltime
:   The real-world clock time a job is allowed to run before the scheduler stops
    it. Request enough, but over-requesting can delay when your job starts.

Allocation
:   The share of computing resources granted to your research group, which your
    jobs draw from.

Service Unit (SU)
:   The accounting unit used to measure and charge cluster usage (roughly, one
    core running for one hour). See [Service Units](slurm/service-units.md).

## Software

Module
:   A pre-installed software package you load into your environment on demand
    with `module load`, rather than installing it yourself. See
    [Discovering Software](getting-started/step3/getting-started-step3-software.md).

Lmod
:   The tool that manages modules, letting you search (`module spider`), load, and
    unload software versions cleanly.

Spack
:   The build system CRCD uses to install and organize software on the current
    (RHEL 9) clusters, which is why modules are named the way they are.

CRC wrappers
:   CRCD's `crc-*` helper commands (for example `crc-interactive`) that simplify
    common Slurm tasks. See [CRC Wrappers](applications/crc-wrappers.md).

## Storage

Home directory (`ihome`)
:   Your personal space on the clusters (under `/ihome/`), shared across all
    nodes and backed up. Good for code and small files, not large I/O. See
    [File Systems](data-management/file-systems.md).

Scratch space
:   Fast temporary storage for the heavy read/write of a running job. Not
    backed up and periodically cleared, so move results out afterward. See
    [Utilizing Scratch Space](slurm/scratch-storage.md).

## Getting connected

PittNet
: the internal University network

Firewall
:A network security device that monitors and filters incoming and outgoing network traffic based on an organization's previously established security policies

VPN (Virtual Private Network)
:   A secure network connection (Pitt's is GlobalProtect) required to reach the
    clusters from off campus. See [Getting an Account](getting-started/getting-started-step1-account.md).

SSH (Secure Shell)
:   The protocol for connecting to a login node from a terminal. See
    [SSH using a terminal](getting-started/terminal.md) and
    [Passwordless SSH](getting-started/passwordless-ssh.md).

Open OnDemand (OOD)
:   A web portal for using the clusters through your browser — files, notebooks,
    and interactive apps with nothing to install. See
    [Open OnDemand](getting-started/open-ondemand.md).
