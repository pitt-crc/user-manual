# PBS to SLURM Commands

PBS Torque and SLURM scripts are two frameworks for specifying the resource requirements and settings for the job you want to run. Frank used PBS Torque for specifying the resource requirement. For the most part, there are equivalent settings in each script. The following table lists examples of equivalent options for PBS and SLURM job scripts.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="cTable">
	<thead>
		<tr>
			<th>Command</th>
			<th>PBS/Torque</th>
			<th>Slurm</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Job submission</td>
			<td>qsub -q &lt;queue&gt; -l nodes=1:ppn=16 -l mem=64g &lt;job script&gt;</td>
			<td>sbatch -p &lt;queue&gt; -N 1 -c 16 --mem=64g &lt;job script&gt;</td>
		</tr>
		<tr>
			<td>Job submission</td>
			<td>qsub &lt;job script&gt;</td>
			<td>sbatch &lt;job script&gt;</td>
		</tr>
		<tr>
			<td>Node count</td>
			<td>-l nodes=&lt;count&gt;</td>
			<td>-N &lt;min[-max]&gt;</td>
		</tr>
		<tr>
			<td>Cores per node</td>
			<td>-l ppn=&lt;count&gt;</td>
			<td>-c &lt;count&gt;</td>
		</tr>
		<tr>
			<td>Memory size</td>
			<td>-l mem=16384</td>
			<td>--mem=16g</td>
		</tr>
		<tr>
			<td>Wall clock limit</td>
			<td>-l walltime=&lt;hh:mm:ss&gt;</td>
			<td>-t &lt;days-hh:mm:ss&gt;</td>
		</tr>
		<tr>
			<td>Job name</td>
			<td>-N &lt;name&gt;</td>
			<td>--job-name=&lt;name&gt;</td>
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

A complete comparison of PBS Torque and SLURM script commands&nbsp;is available.&nbsp; <strong>link to PDF</strong>
