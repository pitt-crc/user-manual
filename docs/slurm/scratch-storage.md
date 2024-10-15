# Utilizing Scratch Space for Faster IO Operations
## Introduction
Scratch storage refers to temporary storage space implemented using high-speed disk drives or SSDs that are typically 
local to the compute nodes. It's designed to provide fast, short-term storage for intermediate or temporary files 
used/generated during the course of computational tasks. When working with Slurm, users can leverage scratch storage to 
enhance the performance of their jobs by reducing I/O overhead. Since scratch storage is located directly on the compute
nodes, it minimizes the time spent transferring data between the nodes and external storage systems, such as network 
file systems (e.g. IX).

In this guide, we will discuss how to utilize scratch space for faster I/O operations in Slurm jobs. The following 
example shows how to utilize scratch space in a Slurm job script to improve the performance of a simple file I/O 
operation:

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
The above script copies input files to the scratch space, sets a trap to copy temp files and 
intermediate outputs back to the submit directory upon job completion, and then runs the job executable on the scratch 
space. Finally, the script copies the output files back to the submit directory. By default, the working directory of 
your job is the directory from which the batch script was submitted.


## Using scratch for multi-node jobs

If your job is running on multiple nodes, then you need to make sure to transfer files to and from the local scratch of all nodes with 'sbcast' and 'sgather'. An example script is shown below:

```shell
#!/bin/bash
#SBATCH --output=job.o%j
#SBATCH --error=job.e%j
#SBATCH --job-name="TestScratch"
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=48
#SBATCH --cluster=mpi
#SBATCH --partition=mpi
#SBATCH --account=sam
#SBATCH --time=00:09:30

module purge

RUNDIR=/bgfs/sam/chx33/simulations
cd $RUNDIR

#echo SLURM_NTASKS $SLURM_NTASKS
#echo $SLURM_SCRATCH

echo 'Project storage path:'  $PWD >> test.in

sbcast test.file $SLURM_SCRATCH/test.in

srun -n $SLURM_NNODES  -N $SLURM_NNODES bash -c 'echo $(hostname):$SLURM_JOB_ID:$SLURM_TASK_PID Local node scratch:$SLURM_SCRATCH >> $SLURM_SCRATCH/scratch.out'
srun -n  $SLURM_NNODES -N $SLURM_NNODES bash -c 'cat test.in >> $SLURM_SCRATCH/scratch.out'

sgather $SLURM_SCRATCH/scratch.out $RUNDIR/copy_scratch

files=($(ls $RUNDIR/copy*))
for file in  ${files[@]} ; do echo "Filename: $file";echo "File content:"; tail $file;  done
```

The job output is as follows, note that the files copied back from the local node scratch to project storage have the node hostname appended to their original filename:

```shell
[chx33@login1 simulations]$ sbatch mpiscratch.slurm
Submitted batch job 2162366 on cluster mpi
[chx33@login1 simulations]$ tail job.o2162366
Filename: /bgfs/sam/chx33/simulations/copy_scratch.mpi-n62.crc.pitt.edu
File content:
mpi-n62.crc.pitt.edu:2162366:20097 Local node scratch:/scratch/slurm-2162366
Project storage path: /bgfs/sam/chx33/simulations
Filename: /bgfs/sam/chx33/simulations/copy_scratch.mpi-n63.crc.pitt.edu
File content:
mpi-n63.crc.pitt.edu:2162366:30979 Local node scratch:/scratch/slurm-2162366
Project storage path: /bgfs/sam/chx33/simulations
```

## Using scratch space with Amber jobs
