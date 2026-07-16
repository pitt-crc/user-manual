# Interactive Jobs

An interactive job gives you a shell on a compute node where you can type
commands live. It's the right tool for testing a smaller version of a job before
you scale up, debugging code, benchmarking, or working through a tutorial for
new software.

!!! warning "Don't run heavy work on the login nodes"
    Login nodes are shared gateways meant for editing files and submitting jobs,
    not for computation — see the
    [Job Scheduling Policy](../policies/job-scheduling-policy.md). An interactive
    job moves that work onto a compute node where it belongs.

!!! tip "Interactive or batch?"
    Interactive sessions are for hands-on, exploratory work. For production runs
    that should proceed unattended, write a [Batch Job](batch-jobs.md) instead.

## Starting an interactive session

### The easy way: `crc-interactive`

The `crc-interactive` helper builds the Slurm request for you. The simplest
invocation grabs the defaults — one core on one SMP node for one hour:

```bash
crc-interactive -s
```

Add flags to request more (cluster, cores, memory, time, GPUs). The full flag
list and a worked example are on
[**Requesting Resources**](../getting-started/step3/getting-started-step3-resources.md).

!!! tip
    Add `-z` to any `crc-interactive` command to print the equivalent `srun`
    command without running it — a good way to learn the raw syntax below.

### The underlying command: `srun`

`crc-interactive` ultimately calls `srun`, which you can also use directly:

```bash
srun -M smp -n1 -t02:00:00 --pty bash
```

This asks the scheduler for a session on the `smp` cluster (`-M`) with one core
(`-n1`) for two hours (`-t02:00:00`), running `bash` in terminal mode
(`--pty`). Any of `smp`, `htc`, `mpi`, or `gpu` can follow `-M` — see the
[`-M` flag note](slurm-overview.md) in the Overview.

When the session starts, your prompt changes from a login node to the assigned
compute node:

```
[kimwong@login1.crc.pitt.edu ~]$ srun -M smp -n1 -t02:00:00 --pty bash
[kimwong@smp-n165.crc.pitt.edu ~]$
```

!!! note "Interactive jobs cost Service Units"
    An interactive session draws [Service Units](service-units.md) from your
    group's allocation for as long as it's held — so exit when you're done. If
    you belong to more than one allocation, choose which to charge with `-A`
    (or `--account`) followed by the group name. To see your default, run
    `sacctmgr show associations onlydefaults | grep <username>`.

## GUI applications (X11 forwarding)

To run a graphical application from the terminal, forward X11 to the compute
node. Allocate a node with `salloc`, then SSH into it with `-X`:

```
salloc -M htc -n1 -t02:00:00
salloc: Granted job allocation 874773
salloc: Waiting for resource configuration
salloc: Nodes htc-n12 are ready for job
```

```bash
ssh -X htc-n12
```

Once connected, load a GUI application with the module system and launch it from
the command line.

!!! note
    The `ssh -X <node>` hop from a login node to your compute node relies on
    passwordless SSH *within* the cluster. If you're prompted for a password or
    denied, set it up following
    [Passwordless SSH → between cluster nodes](../getting-started/passwordless-ssh.md).

For a full graphical desktop, or GUI apps without any X11 setup, the
[Linux Desktop (VIZ)](../getting-started/viz.md) portal is usually easier.

## Open OnDemand (browser-based)

[**Open OnDemand**](../getting-started/open-ondemand.md) runs common GUI tools —
RStudio, Jupyter Notebook, JupyterLab, and MATLAB — in your browser. It's the
easiest option if you're unfamiliar with Slurm or the command line, and the best
way to launch an interactive GUI desktop.

## Quality of Service (QoS)

Every job — interactive or batch — is assigned a Quality of Service that caps
its walltime and influences its scheduling priority. The default is `normal`;
request another with `--qos=<name>`. A job asking for more walltime than its QoS
allows will be rejected.

The QoS levels, their maximum walltimes and priority factors, and the related
per-group CPU/GPU and memory limits are maintained in one place:

!!! info "See Job Limits & QoS for current limits"
    The authoritative tables live on the
    [**Job Limits & QoS**](job-limits.md#group-resource-limits)
    page. Check there rather than relying on a copy here — these limits change,
    and a duplicated table would fall out of date.

## Where to go next

<div class="grid cards" markdown>

-   :material-file-document-edit:{ .lg .middle } __Run work unattended__

    ---

    Turn a tested command into a batch script that runs without you.

    [:octicons-arrow-right-24: Batch Jobs](batch-jobs.md)

-   :material-currency-usd:{ .lg .middle } __Understand the cost__

    ---

    How Service Units are calculated and charged against your allocation.

    [:octicons-arrow-right-24: Service Units](service-units.md)

-   :material-web:{ .lg .middle } __Work in the browser__

    ---

    Notebooks, RStudio, MATLAB, and desktops without command-line setup.

    [:octicons-arrow-right-24: Open OnDemand](../getting-started/open-ondemand.md)

</div>
