# Interactive Jobs

Often times it is prudent to test that a smaller version of your slurm job works correctly before submitting 
the whole thing and to be able to debug your code more interactively.

You may also want to perform benchmarking or work through a tutorial for new software, but without disrupting 
the experience of other users by 
<a href="https://crc.pitt.edu/resources/resources/job-scheduling-policy#:~:text=Login%20nodes%20are%20for%20interactive%20work%20only%3A">running resource intensive commands on the login node</a>.</p>

Interactive jobs can be used to create an instance for this kind of work on a compute node.

## Submitting an Interactive Job
Submitting an interactive job is similar to submitting a batch job, except you use the
<a href="https://slurm.schedmd.com/srun.html">srun</a> command instead of sbatch. For example:

```commandline
srun -n1 -t02:00:00 --pty bash
```

Will prompt the scheduler to start a job on a cluster node with 1 `task` (or `core`, -n1) for 2 hours wall clock 
time (-t02:00:00), and in terminal mode.

When the interactive job starts, you will notice that you are no longer on a login node, but rather one of the 
compute nodes.

```commandline
[fangping@login0a ~]$ srun -n1 -t02:00:00 --pty bash
[fangping@n409 ~]$
```

Interactive jobs draw service units from the slurm allocation that your CRC user is associated with.

!!! note

    If your user account is associated with multiple PI compute resource allocations, you can specify which one your 
    interactive session will charge with the '-A' or '--account' arguments, followed by the group name. To check which 
    account your user charges by default, use the command `sacctmgr show assoc onlydefaults | grep USERNAME`
    Where USERNAME is your username.

## Interactive jobs with x11 forwarding

If you would like to run an application that has a GUI interface, X11 forwarding is required.

You should allocate a job to a node with your parameters, then SSH authenticated X11 session from the login node to 
your interactive session on that compute node. You can follow the following steps:

First, allocate a job
```commandline
salloc -n1 -t02:00:00
salloc: Granted job allocation 874773
salloc: Waiting for resource configuration
salloc: Nodes htc-n1 are ready for job
```
Then use ssh to connect to the node that is ready for you.
```commandline
ssh -X htc-n1
```

Once in your interactive session you can load software that provide GUI with the module system and launch them from 
the command line.

## Open Ondemand

We have implemented [Open Ondemand](../web-portals/open-ondemand/index.md) to run common GUI tools, such as RStudio, 
Jupyter NotebookD, Jupyter Lab and Matlab. This interface is often easier to use if you are unfamiliar with slurm and 
submitting jobs via the command line. This is also the best method for launching an interactive session with a 
GUI desktop.

## Quality of Service
All jobs submitted to Slurm must be assigned a Quality of Service (QoS). QoS levels define resource 
limitations. The default QoS is `normal`.

<table>
	<thead>
		<tr>
			<th>Quality of Service</th>
			<th>Max Walltime</th>
			<th>Priority factor</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>short</td>
			<td>12:00:00</td>
			<td>1.0</td>
		</tr>
		<tr>
			<td>normal</td>
			<td>3-00:00:00</td>
			<td>0.75</td>
		</tr>
		<tr>
			<td>long</td>
			<td>6-00:00:00</td>
			<td>0.5</td>
		</tr>
	</tbody>
</table>

<ul>
	<li>Walltime is specified in days-hh:mm:ss</li>
</ul>

If your job does not meet these requirements it will be not be accepted
