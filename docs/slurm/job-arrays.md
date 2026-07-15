# Job Arrays

You'll often need to submit many similar jobs at once — the same program over
many input files, or one analysis swept across a range of parameters. This is
common in NGS data analysis. A **job array** is the right tool: it submits and
manages a whole collection of near-identical jobs from a single script, far more
efficiently than submitting them in a loop.

## What a job array is

A job array launches many **tasks** from one submission. Every task shares the
same resource request (cores, memory, time); they differ only by an index that
your script reads to pick its input. Arrays are batch-only, and the index range
is set with `--array` (or `-a`):

- `--array=1-10` — tasks 1 through 10
- `--array=0-9` — tasks 0 through 9
- `--array=1,4,7` — specific indices
- `--array=1-10:2` — a step size (1, 3, 5, 7, 9)
- `--array=0-99%4` — cap it at 4 tasks running **concurrently**

Inside the script, three values connect a task to its work:

- `%A` — the array's job ID (used in `--output` filenames)
- `%a` — the task index (used in `--output` filenames)
- `$SLURM_ARRAY_TASK_ID` — the task index, available as a shell variable at runtime

!!! note "Array size limits"
    The maximum array size is **500** on SMP, MPI, and HTC, and **1001** on GPU.
    Per-user and per-account limits also cap how many jobs accrue priority at
    once. Use the `%N` concurrency throttle above to stay friendly to the
    scheduler. See the
    [Job Scheduling Policy](../policies/job-scheduling-policy.md#exceeding-usage-limits-will-cause-job-pending-status).

## A minimal example

This runs 10 tasks, each printing its index and (as a template) processing one
numbered input file:

```bash
#!/bin/bash
#SBATCH --job-name=array-demo
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --nodes=1
#SBATCH --cpus-per-task=1
#SBATCH --time=1-00:00:00
#SBATCH --array=1-10
#SBATCH --output=demo-%A_%a.out

echo "Array task ${SLURM_ARRAY_TASK_ID} of job ${SLURM_ARRAY_JOB_ID}"
# Do this task's work, e.g.:
# ./my_program input_${SLURM_ARRAY_TASK_ID}.dat
```

Submit it like any batch job with `sbatch array-demo.sbatch`. Everything else
about the script — directives, `module load`, etc. — works exactly as on the
[Batch Jobs](batch-jobs.md) page; the only additions are `--array` and the
`%A`/`%a`/`$SLURM_ARRAY_TASK_ID` substitutions.

## Applied examples (NGS)

### Sequentially named files

Suppose a folder holds five paired-end datasets, `SRR098333_1.fastq` …
`SRR098338_2.fastq`, and you want `fastqc` on all of them. Because the sample
numbers run 33–38, an array index of `3-8` maps straight onto the filenames:

```bash
#!/bin/bash
#SBATCH --job-name=fastqc_samples
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --nodes=1
#SBATCH --cpus-per-task=1
#SBATCH --time=3-00:00:00
#SBATCH --array=3-8
#SBATCH --output=fastqc-%A_%a.out

module purge
module load fastqc/0.11.9

mkdir -p ./fastqc_pretrim
echo "parsing sample: SRR09833${SLURM_ARRAY_TASK_ID}"
fastqc -o ./fastqc_pretrim/ SRR09833${SLURM_ARRAY_TASK_ID}_1.fastq
fastqc -o ./fastqc_pretrim/ SRR09833${SLURM_ARRAY_TASK_ID}_2.fastq
```

### Non-sequentially named files

When filenames don't map cleanly to integers, build the name from the index with
`ls`, `head`, and `tail`:

```bash
#!/bin/bash
#SBATCH --job-name=fastqc
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --nodes=1
#SBATCH --cpus-per-task=1
#SBATCH --time=3-00:00:00
#SBATCH --array=1-6
#SBATCH --output=fastqc-%A_%a.out

module purge
module load fastqc/0.11.9

file=$(ls *_1.fastq | head -n $SLURM_ARRAY_TASK_ID | tail -n 1)
echo "parsing sample: $file"
mkdir -p ./fastqc_posttrim
fastqc -o ./fastqc_posttrim/ $file
```

### A name array (bowtie2)

Precompute a list of sample names, then index into it with the task ID:

```bash
ls *_1.fastq | cut -d_ -f1 > jobs      # -> SRR098333, SRR098334, ...
```

```bash
#!/bin/bash
#SBATCH --job-name=bowtie2
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --nodes=1
#SBATCH --cpus-per-task=16
#SBATCH --time=1-00:00:00
#SBATCH --array=0-5
#SBATCH --output=bowtie2-%A_%a.out

module purge
module load bowtie2/2.4.5

names=($(cat jobs))
sample=${names[${SLURM_ARRAY_TASK_ID}]}
echo "$sample"

bowtie2 -p 16 -x /path/to/bowtie2_index \
  -1 ${sample}_1.fastq -2 ${sample}_2.fastq \
  -S alignments/${sample}.bowtie2.sam
```

### A "commands" file (multiple parameters)

When each job needs several parameters that don't map to integers, put one full
command's arguments per line in a text file and select the line with `awk`:

```
[fangping@login1 jobs]$ cat bwa_mem.txt
-Y -R "@RG\tID:Exome_Norm\t..." -o ../results/bwa/Exome_Norm.sam ref.fasta Exome_Norm_R1.fastq.gz Exome_Norm_R2.fastq.gz
-Y -R "@RG\tID:Exome_Tumor\t..." -o ../results/bwa/Exome_Tumor.sam ref.fasta Exome_Tumor_R1.fastq.gz Exome_Tumor_R2.fastq.gz
...  (one line per sample)
```

```bash
#!/bin/bash
#SBATCH --job-name=bwa_human_samples
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=60g
#SBATCH --time=3-00:00:00
#SBATCH --array=1-10
#SBATCH --output=bwa-%A_%a.out

module purge
module load gcc/10.2.0 bwa/0.7.17
mkdir -p ../results/bwa

args=$(awk -v line=$SLURM_ARRAY_TASK_ID 'NR==line' bwa_mem.txt)
echo "bwa mem -t 8 $args"
bash -c "bwa mem -t 8 $args"
```

For fuller, maintained bioinformatics workflows, see the
[RNASeq Data Analysis](../advanced-genomics-support/RNASeq-data-analysis.md)
notes.

## Managing array jobs

In `squeue`, array tasks appear as `JobID_TaskID`. Cancel the entire array with
`scancel <JobID>`, or a single task with `scancel <JobID>_<index>`. Monitoring,
inspecting, and cancelling jobs are covered in
[Managing Jobs](../getting-started/step3/getting-started-step3-manage-jobs.md).

## Alternative: `sbatch --wrap` for one-offs

For a quick batch of unrelated one-off commands, `--wrap` submits a command as a
job without writing a script file. For example, to `gzip` every FASTQ in a
directory:

```bash
#!/bin/bash
for FILE in *.fastq; do
    echo "${FILE}"
    sbatch --cluster=htc --partition=htc -n 1 -t 1-00:00:00 --wrap="gzip ${FILE}"
    sleep 1   # pause to be kind to the scheduler
done
```

Make it executable (`chmod +x run_gzip.sh`) and run it. That said, when the jobs
are variations on one task, a **job array is usually preferable** — one
submission instead of a loop, and much less load on the scheduler (the `sleep 1`
above is a symptom of that load).

## Related

<div class="grid cards" markdown>

-   :material-file-document-edit:{ .lg .middle } __Batch job basics__

    ---

    Directives and script structure that arrays build on.

    [:octicons-arrow-right-24: Batch Jobs](batch-jobs.md)

-   :material-clipboard-check:{ .lg .middle } __Track your tasks__

    ---

    Monitor and cancel array jobs and individual tasks.

    [:octicons-arrow-right-24: Managing Jobs](../getting-started/step3/getting-started-step3-manage-jobs.md)

-   :material-scale-balance:{ .lg .middle } __Array & job limits__

    ---

    Maximum array sizes and per-user/account job limits.

    [:octicons-arrow-right-24: Job Scheduling Policy](../policies/job-scheduling-policy.md)

</div>
