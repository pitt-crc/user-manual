# Scratch Storage

Scratch storage is high-speed local storage (NVMe SSDs) on compute nodes, designed for temporary files during job execution. Using scratch reduces I/O overhead by avoiding network file system traffic.

## Why Use Scratch?

- **Faster I/O**: Local NVMe is significantly faster than network storage (`/ix`, `/ix1`)
- **Reduced contention**: No competition with other users for network bandwidth
- **Better performance**: Ideal for jobs with heavy read/write operations

## Using Scratch in a Job Script

The environment variable `$SLURM_SCRATCH` points to a temporary directory on the compute node's local storage, created automatically for each job.

```bash
#!/bin/bash
#SBATCH --job-name=<job_name>
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=<tasks>
#SBATCH --cluster=<cluster>
#SBATCH --partition=<partition>
#SBATCH --time=<days-HH:MM:SS>

module purge
module load module1 module2

# Copy inputs to fast local scratch
cp <inputs> $SLURM_SCRATCH
cd $SLURM_SCRATCH

# Ensure outputs are copied back even if the job fails
run_on_exit(){ cp -r $SLURM_SCRATCH/* $SLURM_SUBMIT_DIR; }
trap run_on_exit EXIT

# Run the job
srun <executable with parameters>

# Copy outputs back to submission directory
cp <outputs> $SLURM_SUBMIT_DIR
```

!!! warning
    Scratch storage is **ephemeral** - it is wiped after your job ends. Always copy results back to your project directory.

## Multi-Node Jobs

For jobs spanning multiple nodes, use `sbcast` and `sgather` to transfer files to/from local scratch on all nodes:

```bash
#!/bin/bash
#SBATCH --job-name=multi_node_scratch
#SBATCH --nodes=2
#SBATCH --ntasks-per-node=48
#SBATCH --cluster=mpi
#SBATCH --partition=mpi
#SBATCH --time=1-00:00:00

module purge
module load module1 module2

# Broadcast input files to all nodes' local scratch
sbcast input_file.dat $SLURM_SCRATCH/input_file.dat

cd $SLURM_SCRATCH

# Run the multi-node job
srun <executable with parameters>

# Gather outputs from all nodes back to submission directory
sgather -r $SLURM_SCRATCH/output $SLURM_SUBMIT_DIR/output
```

## Key Points

- `$SLURM_SCRATCH` is automatically set by Slurm for each job
- `$SLURM_SUBMIT_DIR` contains the path where `sbatch` was called
- Use `trap` with `EXIT` to ensure data is saved even if the job fails or times out
- For multi-node jobs, `sbcast` distributes files and `sgather` collects results
