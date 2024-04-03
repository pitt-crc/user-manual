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

## Using scratch space with Amber jobs
