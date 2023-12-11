# CRC Wrappers

The CRC Team provides some wrapper scripts that make interacting with SLURM easier by abstracting away some of the details of common commands. The scripts are written in Python and should accept&nbsp;<span style="font-family:courier new,courier,monospace;">-h</span>&nbsp;and&nbsp;<span style="font-family:courier new,courier,monospace;">--help</span>&nbsp;to provide help documentation.

## What commands are available?

<table border="1" cellpadding="1" cellspacing="1" style="width: 684px;">
	<tbody>
		<tr>
			<td style="width: 174px;">Wrapper Command</td>
			<td style="width: 52px;">SLURM Functionality&nbsp;</td>
			<td style="width: 433px;">Explanation</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-sinfo</span></td>
			<td style="width: 52px;"><a href="https://slurm.schedmd.com/sinfo.html">sinfo</a></td>
			<td style="width: 433px;">Oververview of the current hardware status</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-squeue</span></td>
			<td style="width: 52px;"><a href="https://slurm.schedmd.com/squeue.html">squeue</a></td>
			<td style="width: 433px;">
			Provide a more convenient view of your currently running jobs

			<ul>
				<li>crc-squeue&nbsp;--start:&nbsp;Show approximate start time for your jobs, won’t show if you hit association limits</li>
				<li>crc-squeue&nbsp;--watch:&nbsp;Watch your jobs as they progress in time (updates 10 seconds at a time)</li>
				<li>crc-squeue&nbsp;--all:&nbsp;Show all the jobs on the cluster</li>
			</ul>
			</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-scancel</span></td>
			<td style="width: 52px;"><a href="https://slurm.schedmd.com/scancel.html">scancel</a></td>
			<td style="width: 433px;">Cancel the the job with the provided JobID&nbsp;</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-interactive</span></td>
			<td style="width: 52px;"><a href="https://slurm.schedmd.com/srun.html">srun</a></td>
			<td style="width: 433px;">
			<ul>
				<li>Run interactive jobs on the cluster
				<ul>
					<li>
					To submit an interactive job, you should use the CRC wrapper:

					<pre>
crc-interactive --smp --time=1:00 --num-cores=2</pre>

					would give you an interactive job for 1 hour on SMP with 2 processors. When the interactive job starts, you will notice that you are no longer on a login node, but rather one of the compute nodes.

					<pre>
[shs159@smp-n2 ˜]$

Try <span style="font-family:courier new,courier,monospace">crc-interactive -h</span> for more details.</pre>
					</li>
					<li>Note:&nbsp;The Python 2.7 library will be loaded automatically when you run crc-interactive. This will prevent&nbsp;you from&nbsp;sourcing&nbsp;a python 3.x&nbsp;environment. See <a href="https://crc.pitt.edu/resources/htc-cluster/interactive-jobs">this page</a> for an alternative method.&nbsp;</li>
					<li>X11 forwarding is known to cause issues with crc-interactive. If you need an interactive session with X11 forwarding enabled, please refer to <a href="https://crc.pitt.edu/resources/htc-cluster/interactive-jobs#:~:text=Interactive%20jobs%20with%20x11%20forwarding">this documentation</a>.</li>
				</ul>
				</li>
			</ul>
			</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-quota</span></td>
			<td style="width: 52px;">&nbsp;</td>
			<td style="width: 433px;">Show your storage quota on all of our storage systems&nbsp;</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace;">crc-usage</span></td>
			<td style="width: 52px;">&nbsp;</td>
			<td style="width: 433px;">
			Show your usage on each cluster

			<ul>
				<li>For now, this will only show your primary group. Try <span style="font-family:courier new,courier,monospace;">groups | cut -d' ' -f1</span> to find your primary group.</li>
			</ul>
			</td>
		</tr>
		<tr>
			<td style="width: 174px;"><span style="font-family:courier new,courier,monospace">crc-job-stats</span></td>
			<td style="width: 52px;">&nbsp;</td>
			<td style="width: 433px;">
			This script is meant to be added at the bottom of your Slurm scripts (after srun) to give the statististic of your job.
			</td>
		</tr>
	</tbody>
</table>
