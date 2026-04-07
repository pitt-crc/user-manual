# Service Units

One service unit (SU) is approximately equal to 1 core hour of computing. The charge is calculated based on factors that the&nbsp;slurm workload manager calls '<strong>T</strong>rackable <strong>RES</strong>ources' or TRES. The important TRES values for calculating CRCD SUs are:

- Number of cores requested
- RAM requested
- On the GPU cluster, number of cards requested.

Each of these has a TRES Billing Weight assigned to it in the cluster configuration files. 
These weights along with the amount of resources your job is allocated are used to construct a total cost in SUs. 
Here is a table listing the weights for each cluster and partition:

| Cluster | Partition          | Compute Weight (Per CPU/GPU) | Memory Weight (Per GB) |
|---------|--------------------|------------------------------|------------------------|
| SMP     |                    |                              |                        |
|         | smp                | 0.8                          | 0.102                  |
|         | high-mem           | 1.0                          | 0.0477                 |
|         | preempt            | 0                            | 0                      |
| MPI     |                    |                              |                        |
|         | ndr                | 1                            | 0.93                   |
|         | mpi                | 1                            | 0.93                   |
|         | preempt            | 0                            | 0                      |
|         | preempt_ndr        | 0                            | 0                      |
| GPU     |                    |                              |                        |
|         | a100               | 8                            | 0                      |
|         | a100_multi         | 8                            | 0                      |
|         | a100_nvlink        | 8                            | 0                      |
|         | a100_nvlink_multi  | 8                            | 0                      |
|         | l40s               | 8                            | 0                      |
|         | preempt            | 0                            | 0                      |
| HTC     |                    |                              |                        |
|         | htc                | 1                            | 0.128                  |
|         | preempt            | 0                            | 0                      |


To see a more detailed view of this information (including investment hardware configurations), you can use the scontrol slurm command, providing the -M flag to specify a cluster:

```commandline
[nlc60@login1 ~] : scontrol -M htc show partition
PartitionName=htc
   AllowGroups=ALL AllowAccounts=ALL AllowQos=short,normal,long,htc-htc-s,htc-htc-n,htc-htc-l,htc-htc-ll,htc-htc-s-invest,htc-htc-n-invest,htc-htc-l-invest,htc-htc-ll-invest,htc-htc-crunyan-s,htc-htc-crunyan-n,htc-htc-crunyan-l,htc-htc-crunyan-ll
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED MaxCPUsPerSocket=UNLIMITED
   Nodes=htc-1024-n[0-3],htc-n[24-93]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=CANCEL
   State=UP TotalCPUs=6144 TotalNodes=74 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
   TRES=cpu=6144,mem=72007200M,node=74,billing=9000
   TRESBillingWeights=CPU=1.0,Mem=0.128G

PartitionName=preempt
   AllowGroups=ALL AllowAccounts=ALL AllowQos=htc-preempt-s,htc-preempt-n,htc-preempt-l,htc-preempt-ll
   AllocNodes=ALL Default=NO QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED MaxCPUsPerSocket=UNLIMITED
   Nodes=htc-1024-n[0-3],htc-n[24-93]
   PriorityJobFactor=0 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=CANCEL
   State=UP TotalCPUs=6144 TotalNodes=74 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
   TRES=cpu=6144,mem=72007200M,node=74
   TRESBillingWeights=CPU=0,Mem=0.0G
```

For a concise view of the TRES used by a job, you can use the ```sacct``` command:

```commandline
[nlc60@login0b ~] : sacct -X -M smp -j 6169876 --format=User,JobID,Jobname,AllocTRES%30,Elapsed
     User        JobID    JobName                      AllocTRES    Elapsed
--------- ------------ ---------- ------------------------------ ----------
    nlc60 6169876      hello_wor+         cpu=1,mem=4018M,node=1   00:00:01
```
