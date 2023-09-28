# Job Scheduling Policy

## Login Nodes are for Interactive, Non-intensive Work Only

Many users are logged into the login nodes of the H2P and HTC clusters at the same time. These are the gateways everyone uses to perform interactive work like editing code, submitting and checking the status of jobs, etc.

Executing processing scripts or commands on these nodes can cause substantial slowdowns for the rest of the users. For this reason, it is important to make sure that this kind of work is done in either an interactive session on a node from one of the clusters, or as a batch job submission.

Resource-intensive processes found to be running on the login nodes may be killed at anytime.

The CRC team reserves the right to revoke cluster access of any user who repeatedly causes slowdowns on the login nodes with processes that can otherwise be run on the compute nodes 

## Jobs are Subject to Priority Queueing
There are settings in place within the Slurm Workload Manager that allow all groups (and users within those groups) to have equal opportunity to run calculations. This concept is called “Fair Share”. The fair share is a multiplicative factor in computing a job’s “Priority”. At Pitt, we use a multi-factor priority system which includes:

Age [0,1] – Length of time the job been in the queue and eligible to be scheduled. Longer time spent in the queue recieves higher priority. The maximum is attained at a queue time of 7 days. The priority weight for age is 2000.

Job Size [0,1] – Required Nodes, CPUs, and Memory usage. Larger requests recieve increased priority. The maximum corresponds to using all of the resources on the cluster. The prioirty weight for job size is 2000.

Quality of Service (QOS) [0,1] - Factor based on the Walltime of the job. Normalized to the highest (short QOS). The priority weight for QOS is 2000.

| Name     | Priority | QOS Factor | Max Walltime (D-HH:MM:SS) |
|----------|----------|------------|---------------------------|
| `Short`  | 13       | 1.00       | 3-00:00:00                |
| `Normal` | 12       | 0.92       | 3-00:00:00                |
| `Long`   | 11       | 0.84       | 6-00:00:00                |

Note: Due to the high volume nature of the work done on HTC, it's short, normal, and long QOS have a maximum number of submissions per user set to 6000, 5000, and 4000 respectively.

Fair Share [0,1] - A metric for overall usage by allocation that prioritizes jobs from under serviced slurm accounts. Less overall cluster use recieves higher priority. The priority weight for fairshare is 3000.

Priority is computed as a sum of all the individual factors multiplied by their priority weights. Higher priorities will be assigned resources first. Your jobs may recieve pending status with Priority as the reason.

Checking Job Priority
You can use the sprio slurm utility to see the priority of your jobs,


    [nlc60@login0b ~] : sprio -M smp -u nlc60 -j 6136016
          JOBID PARTITION     USER   PRIORITY       SITE        AGE  FAIRSHARE    JOBSIZE        QOS                 
        6136016 smp          nlc60       2272          0          0        269          4       2000ll documentation visit [mkdocs.org](https://www.mkdocs.org).


Walltime Extensions will generally not be Granted
It is up to the job submitter to determine the demands of their job through some benchmarking before submitting, perform any necessary code optimization, and to then specify the memory, cpu, and time requirements accordingly. This ensures that the job is queued with respect to FairShare, and that CRC resources utilized by a job are available to other users within a reasonable time frame. 

Due to the current QOS system, you may have a job that is not restartable, and was originally submitted for 6 days (long QOS), where the only way to keep it going is via extension. Please submit a ticket, providing details about your job and justification for why it should be extended. 

Exceeding Usage Limits will cause Job Pending Status
After submission, a job can appear with a "status" of "PD" (not running).

There are various reasons that SLURM will put your job in a pending state. Some common explanations are listed below.

Reasons related to resource availibility and job dependencies:

| Reason                   | Explanation                                                                                                                          | Resolution                                                                                                                                    |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| Resources                | The cluster is busy and no resources are currently available for your job.                                                           | Your job will run as soon as the resources requested become available                                                                         |
| Priority                 | See section on job priority                                                                                                          | Your job will run as soon as it has reached a high enough priority                                                                            |
| Dependency               | A job cannot start until another job is finished. This only happens if you included a "--dependency" directive in your SLURM script. | Wait until the job that you have marked as a dependency is finished, then your job will run                                                   |
| DependencyNeverSatisfied | A job cannot start because another job on which it depends failed                                                                    | Please cancel this job, as it will never be able to run. You will need to resolve the issues in the job that has been marked as a dependency. |

Reasons related to exceeding a usage limit:
MaxMemoryPerAccount
The job exceeds the current within-group memory quota. The maximum quota available depends on the cluster and partition. The table below gives the maximum memory (in GB) for each QOS in the clusters/partitions it is defined. 

| Cluster | Partition | Short | Normal | Long |
|---------|-----------|-------|--------|------|
| smp     |           |       |        |      |
|         | smp       | 13247 | 11044  | 9999 |
|         | legacy    | 620   | 620    | 620  |
|         | high-mem  | 6512  | 6512   | 6512 |

If you find yourself consistently running into this issue, you can use the 'seff' tool to determine the efficiency of your completed jobs. 

    [nlc60@login0b ~] : srun -M CLUSTER_JOB_RAN_ON seff YOUR_JOBID
    srun: job INTERACTIVE_SESSION_JOBID queued and waiting for resources
    srun: job INTERACTIVE_SESSION_JOBID has been allocated resources
    Job ID: YOUR_JOBID
    Cluster: CLUSTER_JOB_RAN_ON
    User/Group: USERNAME/GROUPNAME
    State: TIMEOUT (exit code 0)
    Cores: 1
    CPU Utilized: 6-00:00:52
    CPU Efficiency: 100.01% of 6-00:00:19 core-walltime
    Job Wall-clock time: 6-00:00:19
    Memory Utilized: 128.58 GB
    Memory Efficiency: 14.29% of 900.00 GB

AssocGrpCPURunMinutesLimit
There are a few possible reasons for this:

Your group's usage (CPURunMinutes for a user = num jobs * remaining walltime (in minutes) * cores) has surpassed the limit. This should resolve after the time remaining on your groups jobs has gone down, or when they finish. 
Your allocation proposal is expired. Check this with crc-usage. You can submit a new proposal or renew for a standard allocation of 10K service units.
Your allocation has been spent completely. You will need so submit a request for supplemental service units. 
Temporary extension of your current SU allocation my be granted while submitted proposals are under review, and can be requested by submitting a ticket.

MaxTRESPerAccount, MaxCpuPerAccount, or MaxGRESPerAccount
In the table below, the group based CPU (GPUs for the gpu cluster) limits are presented for each QOS walltime length.  If your group requests more CPU/GPUs than in this table you will be forced to wait until your group's jobs finish.

| cluster | partition    | short | normal | long |
|---------|--------------|-------|--------|------|
| smp     |              |       |        |      |
|         | smp          | 2304  | 1613   | 1152 |
|         | high-mem     | 320   | 224    | 160  |
| gpu     |              |       |        |      |
|         | gtx1080      | 32    | 20     | 14   |
|         | a100         | 16    | 12     | 8    |
|         | a100_multi   | 32    | 24     | 8    |
|         | a100_nvlink  | 24    | 16     | 8    |  
| mpi     |              |       |        |      |
|         | opa-high-mem | 504   | 353    | 252  |
|         | mpi          | 3264  | 2285   | 1632 |
| htc     |              |       |        |      |
|         | htc          | 1216  | 852    | 608  |

