# Slurm Overview

The CRC clusters use [Slurm](http://slurm.schedmd.com/) for batch job queuing. 
The `sinfo` command provides an overview of the state of the nodes within the cluster.

```commandline
[user@login0 ~ ]$ sinfo -M smp,gpu,mpi
CLUSTER: gpu

PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
gtx1080*     up   infinite      5    mix gpu-stage[08-12]
gtx1080*     up   infinite     13   idle gpu-n[16-25],gpu-stage[13-15]
titanx       up   infinite      1    mix gpu-stage01
titanx       up   infinite      6   idle gpu-stage[02-07]
k40          up   infinite      1   idle smpgpu-n0
titan        up   infinite      5   idle legacy-n[126,128-131]

CLUSTER: mpi
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
opa*         up   infinite      3    mix opa-n[80,89-90]
opa*         up   infinite     47  alloc opa-n[0-9,12,15-23,32-37,39-45,60-64,72-75,81-84,86]
opa*         up   infinite     46   idle opa-n[10-11,13-14,24-31,38,46-59,65-71,76-79,85,87-88,91-95]
legacy       up   infinite     20   idle legacy-n[0-19]

CLUSTER: smp
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
smp*         up   infinite      6    mix smp-n[42,56-58,63,65]
smp*         up   infinite      3  alloc smp-n[44,46,62]
smp*         up   infinite     91   idle smp-n[24-41,43,45,47-55,59-61,64,66-123]
high-mem     up   infinite     29   idle smp-256-n[1-2],smp-512-n[1-2],smp-n[0-23],smp-nvme-n1
```
!!! note

    The `-M` flag for `sinfo`, `scontrol`,`sbatch` and `scancel` specify one or more clusters you want to see the 
    output for. By default (without this flag), all commands refer to the primary cluster configured on the login node 
    you are on (SMP cluster on h2p.crc.pitt.edu, and HTC on htc.crc.pitt.edu).

Nodes in the `alloc` state mean that a job is running. 
The asterisk next to the partition means that it is the default partition for all jobs.

`squeue -M <smp,mpi,gpu,htc>` shows the list of running and queued jobs.

The most common states for jobs in `squeue` are described below. See the output of `man squeue` or 
[this page](https://slurm.schedmd.com/squeue.html#SECTION_JOB-STATE-CODES) for more details.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display" id="wTable">
    <thead>
        <tr>
			<th>Abbreviation</th>
			<th>State</th>
            <th>Description</th>
		</tr>
    </thead>
	<tbody>
		<tr>
			<td>CA</td>
			<td>CANCELLED</td>
            <td>Job was explicitly cancelled by the user or system administrator. The job may or may not have 
                been initiated.</td>
        </tr>
        <tr>
            <td>CD</td>
			<td>COMPLETED</td>
            <td>Job has terminated all processes on all nodes.</td>
        </tr>
        <tr>
            <td>CG</td>
			<td>COMPLETING</td>
            <td>Job is in the process of completing. Some processes on some nodes may still be active.</td>
        </tr>
        <tr>
            <td>F</td>
			<td>FAILED</td>
            <td>Job terminated with non-zero exit code or other failure condition.</td>
        </tr>
        <tr>
            <td>PD</td>
			<td>PENDING</td>
            <td>Job is awaiting resource allocation.</td>
        </tr>
        <tr>
            <td>R</td>
			<td>RUNNING</td>
            <td>Job currently has an allocation.</td>
        </tr>
        <tr>
            <td>TO</td>
			<td>TIMEOUT</td>
            <td>Job terminated upon reaching its time limit.</td>
        </tr>
	</tbody>
</table>

<script type="text/javascript" src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
<script type="text/javascript" src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>
<script type="text/javascript">
  $(document).ready(function() {
    $('#wTable').DataTable({
        "lengthMenu": [ 25, 50, 75, 100 ]
    });
  });
</script>

To see when all jobs are expected to start run `squeue --start`. See `man squeue` for a complete description the 
possible REASONS for pending jobs.
!!! note

    Not all jobs have a definite start time.

The `scontrol` output shows detailed job output for pending or allocated jobs.

```commandline
[user@login0 ~ ]$ scontrol -M <cluster> show job <jobid>
```

