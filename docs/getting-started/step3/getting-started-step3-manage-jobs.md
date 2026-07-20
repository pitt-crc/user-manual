# Managing Computing Jobs

Now that you've crafted a job submission script, how do you submit it and manage
the job while it runs? This page walks through submitting, monitoring,
inspecting, and cancelling a job, using the Amber example from
`/ihome/crc/getting_started/mocvnhlysm_1L40S.1C`. Throughout, `<variable>` marks
a placeholder you replace with your own value.

| Command | Description |
| ------- | ----------- |
| `sbatch <job_script>` | Submit `<job_script>` to the Slurm scheduler |
| `squeue -M <cluster> -u $USER` | Show your active jobs on `<cluster>` |
| `scontrol -M <cluster> show job <JobID>` | Show details about job `<JobID>` |
| `scancel -M <cluster> <JobID>` | Cancel job `<JobID>` |
| `sacct -M <cluster> -j <JobID>` | Look up a *finished* job's accounting record |

!!! tip "Prefer friendlier commands?"
    CRCD provides `crc-*` wrapper commands that simplify these Slurm tasks — see
    [**CRC Wrappers**](../../applications/crc-wrappers.md).

## Submit a job

Submit with `sbatch <job_script>`, where `<job_script>` is a text file of Slurm
directives and commands run top to bottom. The `.slurm` extension is optional,
but adopting a naming convention makes your submission scripts easy to spot.

=== "command"

    ```bash
    sbatch amber.slurm
    ```

=== "output"

    ```
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$sbatch amber.slurm
    Submitted batch job 956929 on cluster gpu
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
    ```

!!! note
    Every submission is assigned a **Job ID** — here, `956929`. You'll use it to
    monitor, inspect, and cancel the job.

## Check job status

Use `squeue -M <cluster> -u $USER`. The `<cluster>` value can be a comma-separated
list of `smp`, `htc`, `mpi`, and `gpu`, or `all` for every cluster. Omitting
`-u $USER` shows *all* users' jobs on the cluster(s).

=== "command"

    ```bash
    squeue -M gpu -u $USER
    ```

=== "output"

    ```
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$squeue -M gpu -u $USER
    CLUSTER: gpu
                 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                956929      l40s   gpus-1  kimwong  R       0:24      1 gpu-n63
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
    ```

!!! note
    This shows job `956929` on the `l40s` partition of the `gpu` cluster
    **Running** (`ST=R`) for 24 seconds on node `gpu-n63`.

The `ST` column reports the job state. The most common values:

| State | Code | Meaning |
| ----- | ---- | ------- |
| Pending | `PD` | Waiting in the queue for resources to free up |
| Running | `R` | Currently executing on a compute node |
| Completing | `CG` | Finishing and releasing its resources |
| Completed | `CD` | Finished successfully (exit code 0) |
| Failed | `F` | Terminated with a non-zero exit code |
| Cancelled | `CA` | Cancelled by you or an administrator |
| Timeout | `TO` | Stopped for exceeding its wall-time limit |

## Inspect a job in detail

`scontrol -M <cluster> show job <JobID>` prints the full record for an active
job. The most useful fields are shown below; expand for the complete output.

=== "command"

    ```bash
    scontrol -M gpu show job 956929
    ```

=== "output (key fields)"

    ```
    JobId=956929 JobName=gpus-1
       UserId=kimwong(15083) GroupId=sam(16036) Account=sam QOS=gpu-l40s-s
       JobState=RUNNING Reason=None
       RunTime=00:01:23 TimeLimit=1-00:00:00
       Partition=l40s NodeList=gpu-n63
       NumNodes=1 NumCPUs=16 NumTasks=1 CPUs/Task=1
       TRES=cpu=16,mem=125G,node=1,billing=8,gres/gpu=1
       Command=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C/amber.slurm
       StdOut=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C/gpus-1.out
       ... (truncated — expand below for all fields)
    ```

??? note "Complete `scontrol` output"
    ```
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$scontrol -M gpu show job 956929
    JobId=956929 JobName=gpus-1
       UserId=kimwong(15083) GroupId=sam(16036) MCS_label=N/A
       Priority=14128 Nice=0 Account=sam QOS=gpu-l40s-s
       JobState=RUNNING Reason=None Dependency=(null)
       Requeue=1 Restarts=0 BatchFlag=1 Reboot=0 ExitCode=0:0
       RunTime=00:01:23 TimeLimit=1-00:00:00 TimeMin=N/A
       SubmitTime=2024-08-14T15:14:09 EligibleTime=2024-08-14T15:14:09
       AccrueTime=2024-08-14T15:14:09
       StartTime=2024-08-14T15:14:09 EndTime=2024-08-15T15:14:09 Deadline=N/A
       PreemptEligibleTime=2024-08-14T15:14:09 PreemptTime=None
       SuspendTime=None SecsPreSuspend=0 LastSchedEval=2024-08-14T15:14:09 Scheduler=Main
       Partition=l40s AllocNode:Sid=login4:29378
       ReqNodeList=(null) ExcNodeList=(null)
       NodeList=gpu-n63
       BatchHost=gpu-n63
       NumNodes=1 NumCPUs=16 NumTasks=1 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
       TRES=cpu=16,mem=125G,node=1,billing=8,gres/gpu=1
       Socks/Node=* NtasksPerN:B:S:C=1:0:*:* CoreSpec=*
       MinCPUsNode=1 MinMemoryCPU=8000M MinTmpDiskNode=0
       Features=(null) DelayBoot=00:00:00
       OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
       Command=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C/amber.slurm
       WorkDir=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C
       StdErr=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C/gpus-1.out
       StdIn=/dev/null
       StdOut=/ihome/crc/getting_started/mocvnhlysm_1L40S.1C/gpus-1.out
       Power=
       CpusPerTres=gpu:16
       TresPerNode=gres:gpu:1

    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
    ```

## Cancel a job

If you spot a mistake after submitting, cancel the job by its `JobID`:

=== "command"

    ```bash
    scancel -M gpu 956929
    ```

=== "output"

    ```
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$scancel -M gpu 956929
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$squeue -M gpu -u $USER
    CLUSTER: gpu
                 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                956929      l40s   gpus-1  kimwong CG       2:47      1 gpu-n63
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$squeue -M gpu -u $USER
    CLUSTER: gpu
                 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
    ```

!!! note
    Right after cancelling, the job briefly shows `ST=CG` (Completing) while it
    releases its resources, then disappears from `squeue` entirely.

## When your job finishes

A completed job **drops out of `squeue`** — that's expected, not a sign it was
lost. To find your results and check what happened:

- **Output files.** Anything your program printed goes to the file named in your
  `#SBATCH --output` directive (here, `gpus-1.out`), in the directory you
  submitted from.
- **Accounting record.** Because `squeue` only lists active jobs, inspect a
  *finished* job with `sacct`:

    ```bash
    sacct -M <cluster> -j <JobID>
    ```

- **Job statistics.** If your script calls the `crc-job-stats` wrapper (as in the
  [**Slurm Batch Jobs**](../../slurm/batch-jobs.md) template), a summary of the
  resources your job used is appended to its output file — useful for
  right-sizing future requests.

!!! success "You've completed Getting Started"
    You can now log in, discover and load software, request resources, and
    submit, monitor, and manage jobs. That's the full onboarding path.

## Where to go next

<div class="grid cards" markdown>

-   :material-file-document-edit:{ .lg .middle } __Write better job scripts__

    ---

    Common directives, CPU/GPU templates, job arrays, and email notifications.

    [:octicons-arrow-right-24: Slurm Batch Jobs](../../slurm/batch-jobs.md)

-   :material-currency-usd:{ .lg .middle } __Track your usage__

    ---

    How Service Units are calculated and charged against your allocation.

    [:octicons-arrow-right-24: Service Units](../../slurm/service-units.md)

-   :material-database:{ .lg .middle } __Manage your data__

    ---

    Where to keep files, quotas, and fast scratch space for heavy I/O.

    [:octicons-arrow-right-24: File Systems](../../data-management/file-systems.md)

-   :material-help-circle:{ .lg .middle } __Get unstuck__

    ---

    Answers to the most common questions and error messages.

    [:octicons-arrow-right-24: FAQ](../../faq.md)

</div>
