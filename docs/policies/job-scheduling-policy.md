# Job Scheduling Policy

## Login Nodes are for Interactive, Non-intensive Work Only

Many users are logged into the login nodes of the H2P and HTC clusters at the same time. These are the gateways everyone 
uses to perform interactive work like editing code, submitting and checking the status of jobs, etc.

Executing processing scripts or commands on these nodes can cause substantial slowdowns for the rest of the users. 
For this reason, it is important to make sure that this kind of work is done in either an interactive session on a node 
from one of the clusters, or as a batch job submission.

Resource-intensive processes found to be running on the login nodes may be killed at anytime.

<ins>**The CRC team reserves the right to revoke cluster access of any user who repeatedly causes slowdowns on the login 
nodes with processes that can otherwise be run on the compute nodes.**</ins>

![JOB-SCHEDULING-POLICY1](../_assets/img/policies/job_scheduling_policy_1.png)

## Jobs are Subject to Priority Queueing
There are settings in place within the Slurm Workload Manager that allow all groups (and users within those groups) 
to have equal opportunity to run calculations. This concept is called “Fair Share”. The fair share is a multiplicative 
factor in computing a job’s “Priority”. At Pitt, we use a multi-factor priority system which includes:

Age [0,1] – Length of time the job been in the queue and eligible to be scheduled. Longer time spent in the queue 
recieves higher priority. The maximum is attained at a queue time of 7 days. The priority weight for age is 2000.

Job Size [0,1] – Required Nodes, CPUs, and Memory usage. Larger requests receive increased priority. The maximum 
corresponds to using all the resources on the cluster. The priority weight for job size is 2000.

Quality of Service (QOS) [0,1] - Factor based on the Walltime of the job. Normalized to the highest (short QOS). 
The priority weight for QOS is 2000.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="nTable">
    <thead>
        <tr>
            <td>Name</td>
            <td>Priority</td>
            <td>QOS Factor</td>
            <td>Max Walltime (D-HH:MM:SS)</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>Short</code></td>
            <td>13</td>
            <td>1.00</td>
            <td>3-00:00:00</td>
        </tr>
        <tr>
            <td><code>Normal</code></td>
            <td>12</td>
            <td>0.92</td>
            <td>3-00:00:00</td>
        </tr>
        <tr>
            <td><code>Long</code></td>
            <td>11</td>
            <td>0.84</td>
            <td>6-00:00:00</td>
        </tr>
    </tbody>
</table>

<script type="text/javascript" src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
<script type="text/javascript" src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>

<script type="text/javascript">
    $(document).ready(function() {
        $('#nTable').DataTable({
            "paging": false,
            "bPaginate": false,
            "bLengthChange": false,
            "bFilter": true,
            "bInfo": false,
            "bAutoWidth": false,
            "searching": false,
            "ordering": false
        });
    });
</script>

Note: Due to the high volume nature of the work done on HTC, it's short, normal, and long QOS have a maximum number of 
submissions per user set to 6000, 5000, and 4000 respectively.

Fair Share [0,1] - A metric for overall usage by allocation that prioritizes jobs from under serviced slurm accounts. 
Less overall cluster use receives higher priority. The priority weight for fairshare is 3000.

Priority is computed as a sum of the individual factors multiplied by their priority weights. Higher 
priorities will be assigned resources first. Your jobs may receive pending status with Priority as the reason.

### Checking Job Priority
You can use the `sprio` slurm utility to see the priority of your jobs
```commandline
[nlc60@login0b ~] : sprio -M smp -u nlc60 -j 6136016
  JOBID PARTITION     USER   PRIORITY       SITE        AGE  FAIRSHARE    JOBSIZE        QOS                 
6136016 smp          nlc60       2272          0          0        269          4       2000
```

## Walltime Extensions will generally not be Granted
It is up to the job submitter to determine the demands of their job through some benchmarking before submitting, 
perform any necessary code optimization, and to then specify the memory, cpu, and time requirements 
accordingly. This ensures that the job is queued with respect to FairShare, and that CRC resources utilized by a job 
are available to other users within a reasonable time frame.

Due to the current QOS system, you may have a job that is not restartable, and was originally submitted for 6 days 
(long QOS), where the only way to keep it going is via extension. Please submit a ticket, providing details about 
your job and justification for why it should be extended. 

## Exceeding Usage Limits will cause Job Pending Status
After submission, a job can appear with a "status" of "PD" (not running).

There are various reasons that SLURM will put your job in a pending state. Some common explanations are listed below.

### Reasons related to resource availability and job dependencies:

<table class="display cell-border" id="aTable">
    <thead>
        <tr>
            <td>Reason</td>
            <td>Explanation</td>
            <td>Resolution</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Resources</td>
            <td>The cluster is busy and no resources are currently available for your job.</td>
            <td>Your job will run as soon as the resources requested become available</td>
        </tr>
        <tr>
            <td>Priority</td>
            <td>See section on job priority</td>
            <td>Your job will run as soon as it has reached a high enough priority</td>
        </tr>
        <tr>
            <td>Dependency</td>
            <td>A job cannot start until another job is finished. This only happens if you included a &quot;--dependency&quot; 
directive in your SLURM script.</td>
            <td>Wait until the job that you have marked as a dependency is finished, then your job will run</td>
        </tr>
        <tr>
            <td>DependencyNeverSatisfied</td>
            <td>A job cannot start because another job on which it depends failed</td>
            <td>Please cancel this job, as it will never be able to run. You will need to resolve the issues in the job 
that has been marked as a dependency.</td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
    $(document).ready(function() {
        $('#aTable').DataTable({
            "paging": false,
            "bPaginate": false,
            "bLengthChange": false,
            "bFilter": true,
            "bInfo": false,
            "bAutoWidth": false,
            "searching": false,
            "ordering": false
        });
    });
</script>

### Reasons related to exceeding a usage limit:
#### MaxMemoryPerAccount
The job exceeds the current within-group memory quota. The maximum quota available depends on the cluster and partition. 
The table below gives the maximum memory (in GB) for each QOS in the clusters/partitions it is defined.

<table class="display cell-border" id="bTable">
    <thead>
        <tr>
            <td>Cluster</td>
            <td>Partition</td>
            <td>Short</td>
            <td>Normal</td>
            <td>Long</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>smp</td>
            <td></td>
            <td></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td></td>
            <td>smp</td>
            <td>13247</td>
            <td>11044</td>
            <td>9999</td>
        </tr>
        <tr>
            <td></td>
            <td>legacy</td>
            <td>620</td>
            <td>620</td>
            <td>620</td>
        </tr>
        <tr>
            <td></td>
            <td>high-mem</td>
            <td>6512</td>
            <td>6512</td>
            <td>6512</td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
    $(document).ready(function() {
        $('#bTable').DataTable({
            "paging": false,
            "bPaginate": false,
            "bLengthChange": false,
            "bFilter": true,
            "bInfo": false,
            "bAutoWidth": false,
            "searching": false,
            "ordering": false
        });
    });
</script>

If you find yourself consistently running into this issue, you can use the `seff` tool to determine the efficiency of 
your completed jobs:
```commandline
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
```

#### AssocGrpCPURunMinutesLimit
There are a few possible reasons for this:

- Your group's usage (CPURunMinutes for a user = num jobs * remaining walltime (in minutes) * cores) has 
  surpassed the limit. This should resolve after the time remaining on your group's jobs has gone down, or when 
  they finish.
- Your allocation proposal is expired. Check this with crc-usage. You can submit a new proposal or renew for a standard 
  allocation of 10K service units.
  Your allocation has been spent completely. You will need so [submit a request for service units](https://crc.pitt.edu/Pitt-CRC-Allocation-Proposal-Guidelines).
  A temporary extension of your current SU allocation may be granted while submitted proposals are under review, and can be 
  requested by submitting a ticket.

#### MaxTRESPerAccount, MaxCpuPerAccount, or MaxGRESPerAccount
In the table below, the group based CPU (GPUs for the gpu cluster) limits are presented for each QOS walltime length. 
If your group requests more CPU/GPUs than in this table you will be forced to wait until your group's jobs finish.

<table class="display cell-border" id="cTable">
    <thead>
        <tr>
            <td>cluster</td>
            <td>partition</td>
            <td>short</td>
            <td>normal</td>
            <td>long</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>smp</td>
            <td>smp</td>
            <td>2304</td>
            <td>1613</td>
            <td>1152</td>
        </tr>
        <tr>
            <td></td>
            <td>high-mem</td>
            <td>320</td>
            <td>224</td>
            <td>160</td>
        </tr>
        <tr>
            <td>gpu</td>
            <td>gtx1080</td>
            <td>32</td>
            <td>20</td>
            <td>14</td>
        </tr>
        <tr>
            <td></td>
            <td>a100</td>
            <td>16</td>
            <td>12</td>
            <td>8</td>
        </tr>
        <tr>
            <td></td>
            <td>a100_multi</td>
            <td>32</td>
            <td>24</td>
            <td>8</td>
        </tr>
        <tr>
            <td></td>
            <td>a100_nvlink</td>
            <td>24</td>
            <td>16</td>
            <td>8</td>
        </tr>
        <tr>
            <td>mpi</td>
            <td>opa-high-mem</td>
            <td>504</td>
            <td>353</td>
            <td>252</td>
        </tr>
        <tr>
            <td></td>
            <td>mpi</td>
            <td>3264</td>
            <td>2285</td>
            <td>1632</td>
        </tr>
        <tr>
            <td>htc</td>
            <td>htc</td>
            <td>1216</td>
            <td>852</td>
            <td>608</td>
        </tr>
    </tbody>
</table>
<script type="text/javascript">
    $(document).ready(function() {
        $('#cTable').DataTable({
            "paging": false,
            "bPaginate": false,
            "bLengthChange": false,
            "bFilter": true,
            "bInfo": false,
            "bAutoWidth": false,
            "searching": false,
            "ordering": false
        });
    });
</script>
