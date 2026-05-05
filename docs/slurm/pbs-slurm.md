# PBS to Slurm Migration

This page provides equivalent commands for users migrating from PBS/Torque (used on the former Frank cluster) to Slurm.

## Command Comparison

| Action | PBS/Torque | Slurm |
|--------|-----------|-------|
| Submit a job | `qsub <job script>` | `sbatch <job script>` |
| Submit with options | `qsub -q <queue> -l nodes=1:ppn=16 -l mem=64g <script>` | `sbatch -p <queue> -N 1 -c 16 --mem=64g <script>` |
| Node count | `-l nodes=<count>` | `-N <min[-max]>` |
| Cores per node | `-l ppn=<count>` | `-c <count>` |
| Memory size | `-l mem=16384` | `--mem=16g` |
| Wall clock limit | `-l walltime=<hh:mm:ss>` | `-t <days-hh:mm:ss>` |
| Job name | `-N <name>` | `--job-name=<name>` |

For the complete Slurm documentation, see the [sbatch man page](https://slurm.schedmd.com/sbatch.html).
