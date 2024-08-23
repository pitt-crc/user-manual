# How to Manage Computing Jobs

??? abstract "Skip to Table of Commands"
    | Command  | Description|
    | :-------------------------------            | :--------------------------------------------------------- |
    | `sbatch <job_script>`                       | Submit `<job_script>` to the Slurm scheduler               |
    | `squeue -M <cluster> -u $USER`              | Display my queued jobs that were submitted to `<cluster>`  |
    | `scontrol -M <cluster> show job <JobID>`    | Display details about job `<JobID>` on `<cluster>`         |
    | `scancel -M <cluster> <JobID>`              | Cancel job `<JobID>` that was submitted to `<cluster>`     |


Now that you have crafted a job submission script, how do you submit it and manage the job? We will use the Amber example, that can be found in

```bash
/ihome/crc/getting_started/mocvnhlysm_1L40S.1C
```

to drive the discussion. The command for submitting a job script is `sbatch <job_script>`, (1) where `<job_script>` is a text file containing
Slurm directives and commands that will be executed from top to bottom. It does not matter if the job submission script ends with a `.slurm`
extension or not. Our recommendation is to adopt a convention to make it simple to spot the job submission script among all your files. To submit
the Amber job to Slurm, execute on the commandline `sbatch amber.slurm`:
{ .annotate }

1.  Throughout the examples, we use the conventional syntax `<variable>` to represent a placeholder for an expected value that the user
    will provide.

!!! example "sbatch &lt;job_script>"

    === "command"
        ```commandline
        sbatch amber.slurm
        ```

    === "output"
        ```bash
        [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$sbatch amber.slurm
        Submitted batch job 956929 on cluster gpu
        [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
        ```

        !!! note
            Every job submission will have associated with it an assigned Job ID. In this example, the Job ID is 956929. 

How do you get a summary on the status of your submitted jobs? The command is `squeue -M <cluster> -u $USER`, where the value for the 
`<cluster>` variable can be any combinations of comma separate list of clusters, including `smp`, `htc`, `mpi`, and `gpu`. The value `all` 
for the `-M` flag will output jobs for all the clusters. If you leave out the `-u $USER` option, `squeue` will output the status for
all jobs on the cluster(s).

!!! example "squeue -M &lt;cluster> -u $USER"

    === "command"
        ```commandline
        squeue -M gpu -u $USER
        ```

    === "output"
        ```bash
        [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$squeue -M gpu -u $USER
        CLUSTER: gpu
                     JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
                    956929      l40s   gpus-1  kimwong  R       0:24      1 gpu-n63
        [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
        ```
        !!! note
            The output shows that job 956929 on the l40s partition of the gpu cluster has been running (ST=R; "the state of job 
            is Running") for 24 seconds on gpu-n63.

To obtain detailed information about a submitted job, you can use the `scontrol` command with the `JobID`:

!!! example "scontrol -M &lt;cluster> show job &lt;JobID>""

    === "command"
        ```commandline
        scontrol -M gpu show job 956929
        ```

    === "output"
        ```bash
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
           Command=/ihome/crc/how_to_run/amber/ZZZ_test_amber24/mocvnhlysm_1L40S.1C/amber.slurm
           WorkDir=/ihome/crc/how_to_run/amber/ZZZ_test_amber24/mocvnhlysm_1L40S.1C
           StdErr=/ihome/crc/how_to_run/amber/ZZZ_test_amber24/mocvnhlysm_1L40S.1C/gpus-1.out
           StdIn=/dev/null
           StdOut=/ihome/crc/how_to_run/amber/ZZZ_test_amber24/mocvnhlysm_1L40S.1C/gpus-1.out
           Power=
           CpusPerTres=gpu:16
           TresPerNode=gres:gpu:1
        
        
        [kimwong@login1.crc.pitt.edu mocvnhlysm_1L40S.1C]$
        ```

Lastly, if you had submitted a job and realized that you have made a mistake in the submission file, you can 
use the `scancel` command to delete job identified by the `JobID`:

!!! example "scancel -M <cluster> <JobID>""
        
    === "command"
        ```commandline
        scancel -M gpu 956929
        ```
        
    === "output"
        ```bash
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
