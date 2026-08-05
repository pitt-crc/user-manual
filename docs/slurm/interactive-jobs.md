# Interactive Jobs

An interactive job gives you a shell on a compute node where you can type
commands live for hands-on, exploratory work. It's the right tool for testing a 
smaller version of a job before you scale up, debugging code, benchmarking, or 
working through a tutorial for new software. The steps below assumes that you have
successfully [**ssh into a login node using a terminal**](../../getting-started/terminal).

## Starting an interactive session

### The easy way: `crc-interactive`

The `crc-interactive` command builds the Slurm request for you. The simplest
invocation grabs the defaults — one core on one SMP node for one hour:

```bash
crc-interactive -s
```

Add flags to request more (cluster, cores, memory, time, GPUs). Pass the command the 
`--help` flag to display the full list of options

=== "command"

    ```bash
    crc-interactive --help
    ```
=== "output"

    ```bash
    usage: crc-interactive [-h] [-v] [-z] [-p PARTITION] [-s] [-g] [-m] [-i] [-d] [-e] [-b MEM] [-t TIME] [-n NUM_NODES] [-c NUM_CORES]
                           [-u NUM_GPUS] [-a ACCOUNT] [-r RESERVATION] [-l LICENSE] [-f FEATURE] [-o]
    
    Launch an interactive Slurm session.
    
    optional arguments:
      -h, --help                                 show this help message and exit
      -v, --version                              show program's version number and exit
      -z, --print-command                        print the equivalent slurm command and exit
    
    Cluster Arguments:
      -p PARTITION, --partition PARTITION        run the session on a specific partition
      -s, --smp                                  launch a session on the smp cluster
      -g, --gpu                                  launch a session on the gpu cluster
      -m, --mpi                                  launch a session on the mpi cluster
      -i, --invest                               launch a session on the invest cluster
      -d, --htc                                  launch a session on the htc cluster
      -e, --teach                                launch a session on the teach cluster
    
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

!!! tip
    Add `-z` to any `crc-interactive` command to print the equivalent `srun`
    command without running it.

    === "command"

        ```bash
        crc-interactive -s -z
        ```

    === "output"

        ``` bash
        srun -M smp --export=ALL --nodes=1 --time=01:00:00 --mem=1g --ntasks-per-node=1 --pty bash
        ```

### The underlying command: `srun`

`crc-interactive` ultimately calls `srun`, which you can also use directly:

```bash
[gnowmik@login1 ~]$ srun -M smp --export=ALL --nodes=1 --time=01:00:00 --mem=1g --ntasks-per-node=1 --pty bash
srun: You have specified NO/WRONG partition, so defaulting to the smp partition.
srun: job 23650051 queued and waiting for resources
srun: job 23650051 has been allocated resources
[gnowmik@smp-n215 ~]$
```

This commmand asks the scheduler for a session on the `smp` cluster (`-M`) with one core
(`--ntasks-per-node`) and 1GB of RAM (`--mem`) on one node (`--nodes`) for one hours (`--time`), running `bash` in terminal mode
(`--pty`). 

When the session starts, your prompt changes from a login node (login1) to the assigned
compute node (smp-n215).

!!! note "Interactive jobs cost Service Units"
    An interactive session draws [Service Units](service-units.md) from your group's Resource 
    Allocation for as long as it's held. Be sure to exit out of the compute node when you're done. 

    If you belong to multiple Resource Allocations, you can select which one to charge against by 
    explicitly specifying `--account=<group>`. To see your default, run

    ```bash
    sacctmgr show associations onlydefaults format=cluster,account%30s,user | grep $USER
    ```

    Remove the `onlydefaults` option to show all your Resource Allocations.

## GUI applications (X11 forwarding)

To run a graphical application from the terminal, you need enable X11 forwarding from a compute
node. First, allocate a node with the `salloc` command, then ssh into it with `-X` flag:

```
[gnowmik@login1 ~]$ salloc -M htc --nodes=1 --time=02:00:00
salloc: You have specified NO/WRONG partition, so defaulting to the htc partition.
salloc: Granted job allocation 10721347
salloc: Waiting for resource configuration
salloc: Nodes htc-n72 are ready for job
[gnowmik@login1 ~]$
```

```bash
[gnowmik@login1 ~]$ ssh -X htc-n72
The authenticity of host 'htc-n72 (10.201.8.72)' can't be established.
ED25519 key fingerprint is SHA256:LSRI8iKoJTxzJprwTWQkr55XuZ/UtTYlk5AtS04Icz0.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'htc-n72' (ED25519) to the list of known hosts.
[gnowmik@htc-n72 ~]$
[gnowmik@htc-n72 ~]$ module load xeyes
[gnowmik@htc-n72 ~]$ xeyes
```

Once connected, load a GUI application with the module system and launch it from
the command line.

![XEYES](../../_assets/img/slurm/xeyes.png)

!!! note
    The `ssh -X <compute_node>` hop from a login node to your compute node relies on
    passwordless SSH *within* the cluster. If you're prompted for a password or
    denied, set it up following
    [Passwordless SSH → between cluster nodes](../getting-started/passwordless-ssh.md).


Remember to `scancel` any interactive job that was started with `salloc`. Otherwise,
the allocated resources remain held until completion of the requested walltime limit.
As shown below, the job remains in the queue in the Running state even after I logged 
out of the compute node.

```bash
[gnowmik@htc-n72 ~]$ module load xeyes
[gnowmik@htc-n72 ~]$ xeyes

^C
[gnowmik@htc-n72 ~]$ exit
logout
Connection to htc-n72 closed.
[gnowmik@login1 ~]$ squeue -M htc -u $USER
CLUSTER: htc
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          10721347       htc interact  gnowmik  R       5:15      1 htc-n72
[gnowmik@login1 ~]$
```

The `scontrol` command shows more details about the allocated interactive job that was 
spawned with `salloc -M htc --nodes=1 --time=02:00:00`. The `--nodes=1` flag allocated
one CPU core on a single node along with the default 8GB of RAM per core for that
particular node.

```bash
[gnowmik@login1 ~]$ scontrol -M htc show job 10721347
JobId=10721347 JobName=interactive
   UserId=gnowmik(152289) GroupId=kwong(16260) MCS_label=N/A
   Priority=14105 Nice=0 Account=kwong QOS=htc-htc-s
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:13:52 TimeLimit=02:00:00 TimeMin=N/A
   SubmitTime=2026-08-02T20:10:30 EligibleTime=2026-08-02T20:10:30
   AccrueTime=Unknown
   StartTime=2026-08-02T20:10:30 EndTime=2026-08-02T22:10:30 Deadline=N/A
   PreemptEligibleTime=2026-08-02T20:10:30 PreemptTime=None
   SuspendTime=None SecsPreSuspend=0 LastSchedEval=2026-08-02T20:10:30 Scheduler=Main
   Partition=htc AllocNode:Sid=login1:400755
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=htc-n72
   BatchHost=htc-n72
   NumNodes=1 NumCPUs=1 NumTasks=1 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   ReqTRES=cpu=1,mem=8000M,node=1,billing=1
   AllocTRES=cpu=1,mem=8000M,node=1,billing=1
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=1 MinMemoryCPU=8000M MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=/bin/bash
   WorkDir=/ihome/kwong/gnowmik
   Power=

[gnowmik@login1 ~]$
```

If you need more, 67 cores for example, you can pass to `salloc` the additional flag `--ntasks-per-node=67`,
and Slurm will schedule the job 

=== "command"
    ```bash
    [gnowmik@login1 ~]$ salloc -M htc --nodes=1 --time=02:00:00 --ntasks-per-node=67
    salloc: You have specified NO/WRONG partition, so defaulting to the htc partition.
    salloc: Granted job allocation 10722043
    salloc: Nodes htc-n87 are ready for job
    [gnowmik@login1 ~]$ scontrol -M htc show job 10722043
    ```
=== "output"

    ```bash
    JobId=10722043 JobName=interactive
       UserId=gnowmik(152289) GroupId=kwong(16260) MCS_label=N/A
       Priority=14084 Nice=0 Account=kwong QOS=htc-htc-s
       JobState=RUNNING Reason=None Dependency=(null)
       Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
       RunTime=00:00:18 TimeLimit=02:00:00 TimeMin=N/A
       SubmitTime=2026-08-03T00:50:00 EligibleTime=2026-08-03T00:50:00
       AccrueTime=Unknown
       StartTime=2026-08-03T00:50:00 EndTime=2026-08-03T02:50:00 Deadline=N/A
       PreemptEligibleTime=2026-08-03T00:50:00 PreemptTime=None
       SuspendTime=None SecsPreSuspend=0 LastSchedEval=2026-08-03T00:50:00 Scheduler=Main
       Partition=htc AllocNode:Sid=login1:370003
       ReqNodeList=(null) ExcNodeList=(null)
       NodeList=htc-n87
       BatchHost=htc-n87
       NumNodes=1 NumCPUs=67 NumTasks=67 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
       ReqTRES=cpu=67,mem=536000M,node=1,billing=67
       AllocTRES=cpu=67,mem=536000M,node=1,billing=67
       Socks/Node=* NtasksPerN:B:S:C=67:0:*:* CoreSpec=*
       MinCPUsNode=67 MinMemoryCPU=8000M MinTmpDiskNode=0
       Features=(null) DelayBoot=00:00:00
       OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
       Command=/bin/bash
       WorkDir=/ihome/kwong/gnowmik
       Power=
    ```
You see in the output to `scontrol` that the job was allocated `NumNodes=1 NumCPUs=67 NumTasks=67`
and with `mem=536000M`, which is the aggregate RAM for 67 cores at 8GB/core.

## GUI applications (Open OnDemand and Viz)

[**Open OnDemand**](../getting-started/open-ondemand.md) runs common GUI tools —
RStudio, Jupyter Notebook, JupyterLab, and MATLAB — in your browser. It's the
easiest option if you're unfamiliar with Slurm or the command line.

Alternatively, for a full graphical Linux desktop, the 
[**VIZ portal**](../getting-started/viz.md) gives you GUI access using a web browser.

## Quality of Service (QoS)

Every job — interactive or batch — is assigned a Quality of Service that caps
its walltime and influences its scheduling priority. The default is `normal`;
request another with `--qos=<name>`. A job asking for more walltime than its QoS
allows will be rejected.

!!! info "See Job Limits & QoS for current limits"
    The QoS levels, their maximum walltimes and priority factors, and the related
    per-group CPU/GPU and memory limits are maintained in the
    [**Job Limits & QoS**](job-limits.md#group-resource-limits) page.

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
