Alpha Fold 3

/ix1/bioinformatics/alphafold3/job.sbatch

```commandline
#!/bin/bash
#SBATCH --job-name=alphafold3
#SBATCH -M gpu
#SBATCH --partition=l40s
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=16
#SBATCH --mem=256GB
#SBATCH --gres=gpu:1
#SBATCH --time=1-00:00:00

module load alphafold/3.0.0

mkdir -p output

run_alphafold.py \
     --json_path=input.json \
     --model_dir=models \
     --db_dir=/ix1/genomics/refs/alphafold/alphafold3 \
     --output_dir=output
```
