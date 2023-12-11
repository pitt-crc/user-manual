# Basic Commands
These are some of the basic commands:

*   sinfo – Quick view of hardware allocated and free
*   smap – More visual version of sinfo using ncurses
*   sview – Graphical interface of hardware (requires X11).
*   sbatch <job_script> – Submit a job file
*   squeue – View all running jobs
*   squeue -u <user> – View particular <user>’s jobs (could be you)
*   sshare – View fairshare information
*   sprio – View queued job’s priority

If you are a PBS Torque user and want to migrate to Slurm, you can find the equivalent examples for PBS and Slurm job scripts in the following table.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="nTable">
	<thead>
        <tr>
			<td>Command</td>
			<td>PBS/Torque</td>
			<td>Slurm</td>
		</tr>
    </thead>
    <tbody>
		<tr>
			<td>Job submission</td>
			<td>qsub job_script</td>
			<td>sbatch job_script</td>
		</tr>
		<tr>
			<td>Job submission</td>
			<td>qsub -q queue -l nodes=1:ppn=16 -l mem=64g job_script</td>
			<td>sbatch --partition=queue --nodes=1 --cpus-per-node=16 --mem=64g job_script</td>
		</tr>
		<tr>
			<td>Node count</td>
			<td>-l nodes=count</td>
			<td>--nodes=1</td>
		</tr>
		<tr>
			<td>Cores per node</td>
			<td>-l ppn=count</td>
			<td>--cpus-per-node=count</td>
		</tr>
		<tr>
			<td>Memory size</td>
			<td>-l mem=16384</td>
			<td>--mem=16g</td>
		</tr>
		<tr>
			<td>Wall clock limit</td>
			<td>-N name&nbsp;</td>
			<td>--job-name=name</td>
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

The ```sbatch``` arguments here are the minimal subset required to accurately specify a job on the h2p cluster. Please refer to man sbatch for more options.

<table class="display cell-border" id="mTable">
	<thead>
        <tr>
			<td>SBATCH ARGUMENT</td>
			<td>DESCRIPTION</td>
		</tr>
    </thead>
    <tbody>
		<tr>
			<td>--nodes</td>
			<td>Maximum number of nodes to be used by each Job Step.</td>
		</tr>
		<tr>
			<td>--tasks-per-node</td>
			<td>Specify the number of tasks to be launched per node.</td>
		</tr>
		<tr>
			<td>--cpus-per-task</td>
			<td>Advise the Slurm controller that ensuing job steps will require a certain number of processors per task.</td>
		</tr>
		<tr>
			<td>--error</td>
			<td>File to redirect standard error</td>
		</tr>
		<tr>
			<td>--job-name</td>
			<td>The job name.</td>
		</tr>
		<tr>
			<td>--time</td>
			<td>Define the total time required for the job<br />
			The format is&nbsp;days-hh:mm:ss.</td>
		</tr>
		<tr>
			<td>--cluster</td>
			<td>Select the cluster to submit the job to smp, mpi and gpu are the available partition in the H2P</td>
		</tr>
		<tr>
			<td>--partition</td>
			<td>Select the partition to submit the job to. smp, high-mem for smp cluster, opa, legacy for mpi cluster, gtx1080, titan, titanx and k40 for gpu cluster.</td>
		</tr>
		<tr>
			<td>--account</td>
			<td>Charge resources used by this job to specified account. This is only relevant for users who are in multiple SLURM accounts because he/she is in groups that are collaborating.</td>
		</tr>
	</tbody>
</table>

<script type="text/javascript">
    $(document).ready(function() {
        $('#mTable').DataTable({
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

```srun``` also takes the ```--nodes```,```--tasks-per-node``` and ```--cpus-per-task``` arguments to allow each job step to change the utilized resources but they cannot exceed those given to sbatch. The above arguments can be provided in a batch script by preceding them with #SBATCH. Note that the shebang (#!) line must be present. The shebang line can call any shell or scripting language available on the cluster. For example, ```#!/usr/bin/env``` bash.

Slurm is very explicit in how one requests cores and nodes. While extremely powerful, the three flags,```--nodes```, ```--ntasks```, and ```--cpus-per-task``` can be a bit confusing at first.
```
--ntasks vs. --cpus-per-task
```
The term “task” in this context can be thought of as a “process”. Therefore, a multi-process program (e.g. MPI) is comprised of multiple tasks. In Slurm, tasks are requested with the ```--ntasks``` flag. A multi-threaded program is comprised of a single task, which can in turn use multiple CPUs. CPUs, for the multithreaded programs, are requested with the ```--cpus-per-task``` flag. Individual tasks cannot be split across multiple compute nodes, so requesting a number of CPUs with ```--cpus-per-task``` flag will always result in all your CPUs allocated on the same compute node.
