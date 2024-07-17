# Service Units

One service unit (SU) is approximately equal to 1 core hour of computing. The charge is calculated based on factors that the&nbsp;slurm workload manager calls '<strong>T</strong>rackable <strong>RES</strong>ources' or TRES. The important TRES values for calculating CRC SUs are:

- Number of cores requested
- RAM requested
- On the GPU cluster, number of cards requested.

Each of these has a TRES Billing Weight assigned to it in the cluster configuration files. These weights along with the amount of resources your job is allocated are used to construct a total cost in SUs. &nbsp;Here is a table listing the weights for each cluster and partition:

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="cTable">
	<thead>
		<tr>
			<th scope="col">Cluster</th>
			<th scope="col">Partition</th>
			<th scope="col">CPU/GPU Weight</th>
			<th scope="col">Memory Weight</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>SMP</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>smp</td>
			<td>0.8</td>
			<td>0.102</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>high-mem</td>
			<td>1.0</td>
			<td>0.0477</td>
		</tr>
		<tr>
			<td>MPI</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>opa-high-mem</td>
			<td>1</td>
			<td>0.149</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>mpi</td>
			<td>1</td>
			<td>0.93</td>
		</tr>
		<tr>
			<td>GPU</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>gtx1080</td>
			<td>1</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>v100</td>
			<td>5</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>power9</td>
			<td>5</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>a100</td>
			<td>8</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>a100_multi</td>
			<td>8</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>a100_nvlink</td>
			<td>8</td>
			<td>0</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>l40s</td>
			<td>8</td>
			<td>0</td>
		</tr>
		<tr>
			<td>HTC</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
			<td>&nbsp;</td>
		</tr>
		<tr>
			<td>&nbsp;</td>
			<td>htc</td>
			<td>1</td>
			<td>0.128</td>
		</tr>
	</tbody>
</table>
<script type="text/javascript" src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
<script type="text/javascript" src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>
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

To see a more detailed view of this information (including investment hardware configurations), you can use the scontrol slurm command, providing the -M flag to specify a cluster:

```commandline
[nlc60@login0b ~] : scontrol -M htc show partition
PartitionName=htc
   AllowGroups=ALL AllowAccounts=ALL AllowQos=short,normal,long,htc-htc-s,htc-htc-n,htc-htc-l,htc-htc-s-invest,htc-htc-n-invest,htc-htc-l-invest
   AllocNodes=ALL Default=YES QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=htc-1024-n[0-3],htc-n[24-49]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=CANCEL
   State=UP TotalCPUs=1792 TotalNodes=30 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
   TRES=cpu=1792,mem=19587200M,node=30,billing=2448
   TRESBillingWeights=CPU=1.0,Mem=0.128G

PartitionName=scavenger
   AllowGroups=ALL AllowAccounts=ALL AllowQos=short,normal,long,htc-scavenger-s,htc-scavenger-n,htc-scavenger-l
   AllocNodes=ALL Default=NO QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=1 MaxTime=UNLIMITED MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=htc-n[24-31]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=CANCEL
   State=UP TotalCPUs=384 TotalNodes=8 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerNode=UNLIMITED MaxMemPerNode=UNLIMITED
   TRES=cpu=384,mem=6180000M,node=8
   TRESBillingWeights=CPU=0.0,Mem=0.0G
```

For a concise view of the TRES used by a job, you can use the ```sacct``` command:

```commandline
[nlc60@login0b ~] : sacct -X -M smp -j 6169876 --format=User,JobID,Jobname,AllocTRES%30,Elapsed
     User        JobID    JobName                      AllocTRES    Elapsed
--------- ------------ ---------- ------------------------------ ----------
    nlc60 6169876      hello_wor+         cpu=1,mem=4018M,node=1   00:00:01
```
