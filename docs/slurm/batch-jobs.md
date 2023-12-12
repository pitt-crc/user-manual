# SLURM Batch Jobs
A brief introduction to SLURM batch jobs and a simple example job can be found in our  
[Getting Started section](https://crc.pitt.edu/getting-started/running-jobs-slurm).

This page will provide lower level details related to SLURM jobs, as well as a more complex example submission script.

## SLURM Batch Job Arguments

Below are a subset of sbatch arguments that can be used to specify a job on the cluster. You do not need to include 
them all. 

Please refer to the [slurm documentation on sbatch](https://slurm.schedmd.com/sbatch.html) for more options.
<table class="display cell-border" id="nTable">
    <thead>
        <tr>
            <td>Argument</td>
            <td>Description</td>
            <td>Format</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td><code>--job-name</code></td>
            <td>The job name. This will appear when you check the status with squeue.</td>
            <td>Something descriptive enough to easily identify and differentiate jobs with. Default is the JobID.</td>
        </tr>
        <tr>
            <td><code>--nodes</code></td>
            <td>Maximum number of nodes to be used.</td>
            <td>Usually 1, MPI requires a minimum of 2. Default is 1.</td>
        </tr>
        <tr>
            <td><code>--ntasks, --ntasks-per-node</code></td>
            <td>Specify the maximum number of tasks to be launched per node.</td>
            <td>Default is 1.</td>
        </tr>
        <tr>
            <td><code>--cluster</code></td>
            <td>The cluster that the job will run on.</td>
            <td>smp, mpi, gpu, htc</td>
        </tr>
        <tr>
            <td><code>--partition</code></td>
            <td>The partition of the cluster that the job will run on..</td>
            <td>See Node Configurations: https://crc.pitt.edu/resources/computing-hardware.</td>
        </tr>
        <tr>
            <td><code>--time</code></td>
            <td>Define the max walltime required for the job.</td>
            <td>days-hh:mm:ss</td>
        </tr>
        <tr>
            <td><code>--qos</code></td>
            <td>Declare the Quality of Service to be used.The default is normal. 
                You need to specify `long` if the walltime is greater than 3 days.</td>
            <td>short, normal, long</td>
        </tr>
        <tr>
            <td><code>--error</code></td>
            <td>File to redirect standard error.</td>
            <td>full path or filename to be written to working directory.</td>
        </tr>
        <tr>
            <td><code>--mem</code></td>
            <td>Memory limit per compute node.</td>
            <td>Memory in MB</td>
        </tr>
        <tr>
            <td><code>--mail-user</code></td>
            <td>Email address for notifications.</td>
            <td>PittID@pitt.edu</td>
        </tr>
        <tr>
            <td><code>--mail-type</code></td>
            <td>Conditions for sending notifications.</td>
            <td>END for when the job finishes, FAIL for if the job fails while running.</td>
        </tr>
        <tr>
            <td><code>--cpus-per-task</code></td>
            <td>Advise the SLURM controller that ensuing job steps will require ncpus number of processors per task.</td>
            <td> Use this to facillitate multithreading.</td>
        </tr>
        <tr>
            <td><code>--gres</code></td>
            <td>Specify usage of a general resource. This is used on GPU cluster jobs to indicate number of cards needed.</td>
            <td>--gres=gpu:1 for 1 card. Required when submitting to the GPU cluster.</td>
        </tr>
    </tbody>
</table>

		
    
		
		
		
	


	


## A More Complex SLURM Submission Script Example

Below is a more abstracted example that loads some modules from the module systems, copies inputs and outputs, etc.

```shell
#!/bin/bash                                     
#SBATCH --job-name=<job_name>                  
#SBATCH --nodes=<number of nodes> 
#SBATCH --ntasks-per-node=<tasks per node> 
#SBATCH --cluster=<cluster name> 
#SBATCH --partition=<partition>            
#SBATCH --mail-user=<user_ID>@pitt.edu    
#SBATCH --mail-type=END,FAIL               
#SBATCH --time=<days-HH:MM:SS>                  
#SBATCH --qos=<qos>                         

module purge                                    
module load module1 module2 

cp <inputs> $SLURM_SCRATCH                      
cd $SLURM_SCRATCH  
run_on_exit(){ cp -r $SLURM_SCRATCH/* $SLURM_SUBMIT_DIR 
} 
trap run_on_exit EXIT 

srun <job executable with parameters>           

crc-job-stats.py                                 

cp <outputs> $SLURM_SUBMIT_DIR 
```

### Specify the interpreter
A shebang (#!) line must be present. The shebang line can call any shell or scripting language available on the cluster.
For example `#!/bin/bash`, `#!/bin/tcsh`, `#!/bin/env python` or `#!/bin/env perl`.
The sbatch arguments are provided in a batch script by preceding them with `#SBATCH`.
The resource specific arguments (`ntasks`, `mem`, `nodes`, `time`) specify the limits of your jobs resources. 

### Module loading
You'll need to specify which modules your job requires. See the 
[module system page](../applications/application-environment.md) for more details on searching for 
available software.

It's always a good idea to perform a `module purge` first to make sure the environment is clean.

### Input Handling
After you load your modules, you can automate any other setup you need to adjust the job's running environment or get 
your input ready. In the case of the example, this is copying input data to a scratch location, and setting up a trap 
to copy temp files and intermediate outputs. By default, the working directory of your job is the directory from which 
the batch script was submitted. You can use the 
[sbatch argument `--chdir`](https://slurm.schedmd.com/sbatch.html#OPT_chdir) to adjust this.

### Start Parallel Job with srun
`srun` can be used to start your job. It also takes the --nodes, --tasks-per-node and --cpus-per-task arguments to allow 
each job step  to change the utilized resources, but they cannot exceed those given to sbatch.

### Report Job Statistics 

Add a call to the `crc-job-stats.py` wrapper script to display statistics for your job. 


### Output Handling

Automate any manipulation of the job's output files at the end of the script.

### Interacting with your Job after Submission
[`scancel`](https://slurm.schedmd.com/scancel.html) - Cancel a job, job array, or job step.

[`squeue`](https://slurm.schedmd.com/squeue.html) - View information about jobs in the queue.

## Frequently Asked Questions

### Q: I don't receive any email notifications about my job despite having supplied --mail-type and --mail-user, what gives?
A: This most frequently due to users providing only their username, and not the following domain in their SBATCH directive.
You **need** to provide your email as `PITTID@pitt.edu` for emails to be sent correctly. In more rare cases, the queue 
for emails can be filled by an individual user by submitting many jobs. This should eventually be resolved on its own, 
but it can be avoided by using proper etiquette when submitting a large quantity of jobs, and removing the email 
notification directive for all/many of the submissions and monitoring more interactively with `squeue`. 

### Q: Where can I find more specific examples of these batch scripts?
A: Example Jobs utilizing commonly loaded modules can be found in `/ihome/crc/how_to_run`
For users performing NGS analyses on HTC, see Dr. Fangping Mu's 
[extensive notes on this page](../advanced-genomics-support/RNASeq-data-analysis.md). 

### Q: I'm confused by the interaction between nodes, tasks, and cpus-per-task. How does that work?
A: Slurm is very explicit in how one requests resources. 
A node is a compute node in the cluster. The default partition of the SMP cluster has many nodes for example. 
The term task in this context can be thought of as a “process”, and is related to the number of CPUs/cores you request.

Say you've specifed `--ntasks=16`:
The first case is that it's for a HTC/SMP/GPU job with 16 independent processes. 
The implicit configuration here is `--nodes=1`, `--ntasks=16` => `--ntasks-per-node=16`

A second case is that you are running a multi-process program on MPI. (--nodes=<some number greater than or equal to 2>)
`--ntasks=16` alone means your program will create a maximum of 16 processes, but you don't care how the cores 
are distributed.

You can use `--ntasks-per-node` to get more specific about what number of tasks you want running across the number of 
nodes you're requesting.

Another case is that you have a multi-threaded program, that is a single task, but can use multiple cores:
`--ntasks=1`, `--cpus-per-task=16`
This is using `--cpus-per-task` to specify that you have 1 task that can be completed by multithreading across 16 cores.
On HTC, SMP, and GPU, individual tasks cannot be split across multiple compute nodes, so requesting a number of CPUs 
with `--cpus-per-task` flag will always result in all your CPUs allocated on the same compute node.

### Q: Slurm doesn't seem to be taking my .bashrc additions into account. How do I prompt this?
A: Slurm does not source `~/.bashrc` or `~/.profile` by default. If your job submission requires environment setting 
specified by these files, you should include `source ~/.bashrc`
in your job submission script after any `module load ...` commands.

### Q: I want to submit a job, but I am unsure what allocation I am drawing resources from. How do I check this?
A: This can be determined with the `sacctmgr` (slurm account manager) command.
`sacctmgr show associations onlydefaults | grep USERNAME`
Where USERNAME is your CRC username.
The output will show, for each cluster (first column), which allocation (second column, refered to as GROUPNAME below) 
is charged from by default.

!!!note
    
    If your user account is associated with multiple PI compute resource allocations, you can run the command above without 
    the `onlydefaults` argument to list all of them, and then specify which one your job will charge with 
    the `-A` or `--account=` arguments followed by the group name.
    
    `#SBATCH --account=GROUPNAME` # Charge GROUPNAME instead of the default.


### Q: My analyses require that I run the same job on many input files, or the same input with many sets of 
### parameters. Is there a better way to submit collections of jobs?
A: Yes, please see this documentation on [submitting multiple jobs](./job-arrays.md).