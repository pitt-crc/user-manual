# CRC Wrappers

The CRC Team provides some wrapper scripts that make interacting with SLURM easier by abstracting away some of the details of common commands. The scripts are written in Python and should accept;<span style="font-family:courier new,courier,monospace;">-h</span>;and;<span style="font-family:courier new,courier,monospace;">--help</span>;to provide help documentation.

## What commands are available?

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="wrapperTable">
	<thead>
		<tr>
			<td>Wrapper Command</td>
			<td>SLURM Functionality</td>
			<td>Explanation</td>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>crc-sinfo</td>
			<td><a href="https://slurm.schedmd.com/sinfo.html">sinfo</a></td>
			<td>Oververview of the current hardware status</td>
		</tr>
		<tr>
			<td>crc-squeue</td>
			<td><a href="https://slurm.schedmd.com/squeue.html">squeue</a></td>
			<td>Provide a more convenient view of your currently running jobs
			<ul>
				<li>crc-squeue;--start:;Show approximate start time for your jobs, won’t show if you hit association limits</li>
				<li>crc-squeue;--watch:;Watch your jobs as they progress in time (updates 10 seconds at a time)</li>
				<li>crc-squeue;--all:;Show all the jobs on the cluster</li>
			</ul>
			</td>
		</tr>
		<tr>
			<td>crc-scancel</td>
			<td><a href="https://slurm.schedmd.com/scancel.html">scancel</a></td>
			<td>Cancel the the job with the provided JobID;</td>
		</tr>
		<tr>
			<td>crc-interactive</span></td>
			<td><a href="https://slurm.schedmd.com/srun.html">srun</a></td>
			<td>
			<ul>
				<li>Run interactive jobs on the cluster
				<ul>
					<li>To submit an interactive job, you should use the CRC wrapper:
						<code>
							crc-interactive --smp --time=1:00 --num-cores=2
 						</code>
						would give you an interactive job for 1 hour on SMP with 2 processors. When the interactive job starts, you will notice that you are no longer on a login node, but rather one of the compute nodes.<br />
						<code>
							[shs159@smp-n2 ˜]$
						</code><br />
						Try 
						<code>
							crc-interactive -h 
						</code>
						for more details.
					</li>
					<li>Note:;The Python 2.7 library will be loaded automatically when you run crc-interactive. This will prevent;you from;sourcing;a python 3.x;environment. See <a href="../../slurm/interactive-jobs">this page</a> for an alternative method.;</li>
					<li>X11 forwarding is known to cause issues with crc-interactive. If you need an interactive session with X11 forwarding enabled, please refer to <a href="../../slurm/interactive-jobs/#interactive-jobs-with-x11-forwarding">this documentation</a>.</li>
				</ul>
				</li>
			</ul>
			</td>
		</tr>
		<tr>
			<td>crc-quota</td>
			<td></td>
			<td>Show your storage quota on all of our storage systems;</td>
		</tr>
		<tr>
			<td>crc-usage</td>
			<td></td>
			<td>Show your usage on each cluster
				<ul>
					<li>For now, this will only show your primary group. Try <code>groups | cut -d' ' -f1</code>code> to find your primary group.</li>
				</ul>
			</td>
		</tr>
		<tr>
			<td>crc-job-stats</td>
			<td></td>
			<td>This script is meant to be added at the bottom of your Slurm scripts (after srun) to give the statististic of your job.</td>
		</tr>
	</tbody>
</table>



<script type="text/javascript" src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
<script type="text/javascript" src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>

<script type="text/javascript">
    $(document).ready(function() {
        $('#wrapperTable').DataTable({
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