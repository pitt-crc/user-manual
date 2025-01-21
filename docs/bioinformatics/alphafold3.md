# Alpha Fold 3

This package provides an implementation of the inference pipeline of AlphaFold 3. We have installed the tool as a module alphafold/3.0.0.
```commandline
module load alphafold/3.0.0
```
## Obtaining Genetic Databases

This step requires wget and zstd to download the Genetic Databases. We have downloaded the databases under /ix1/genomics/refs/alphafold/alphafold3.

## Obtaining Model Parameters
To request access to the AlphaFold 3 model parameters, please complete [this form](https://forms.gle/svvpY4u2jsHEwWYS6). Access will be granted at Google DeepMind’s sole discretion. We will aim to respond to requests within 2–3 business days. You may only use AlphaFold 3 model parameters if received directly from Google. Use is subject to these [terms of use](https://github.com/google-deepmind/alphafold3/blob/main/WEIGHTS_TERMS_OF_USE.md).

## AlphaFold 3 Input

You can provide inputs to run_alphafold.py in one of two ways:

* Single input file: Use the --json_path flag followed by the path to a single JSON file.
* Multiple input files: Use the --input_dir flag followed by the path to a directory of JSON files.

AlphaFold 3 uses a [custom JSON input format](https://github.com/google-deepmind/alphafold3/blob/main/docs/input.md).

This is an example input: /ix1/bioinformatics/alphafold3/input.json

This is /ix1/bioinformatics/alphafold3/job.sbatch

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
