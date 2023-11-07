# Submitting Multiple Jobs to the Clusters

Submitting Multiple Jobs to the Clusters
========================================

One will often need to submit multiple jobs to the clusters for various reasons: To submit a collection of similar jobs on different input files, to vary parameters within one analysis, etc. This is particularly prevalent in NGS data analysis. The instructions below aim to help you take advantage of slurm arrays and shell resources.

Job arrays
----------

Job arrays offer a mechanism for submitting and managing collections of similar jobs quickly and easily; job arrays with millions of tasks can be submitted in milliseconds (subject to configured size limits). All jobs must have the same initial options (e.g. size, time limit, etc.)

Job arrays are only supported for batch jobs and the array index values are specified using the --array or -a option of the sbatch command. The option argument can be specific array index values, a range of index values, and an optional step size as shown in the examples below.

Assume that one has a folder with 5 paired end Illumila data set. The file names are ```SRR098333_1.fastq```, ```SRR098333_2.fastq```, ```SRR098334_1.fastq```, ```SRR098334_2.fastq```, …, ```SRR098338_1.fastq```, ```SRR098338_2.fastq```. One would like to perform fastqc on all files. create a SLURM batch file ```fastqc.sbatch```:

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J fastqc\_samples
#SBATCH --output=fastqc-%A\_%a.out
#SBATCH --array=3-8 # job array index
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load FastQC/0.11.5
 
echo "parsing sample: SRR09833"${SLURM\_ARRAY\_TASK\_ID}
 
fastqc -o ./fastqc\_pretrim/ SRR09833${SLURM\_ARRAY\_TASK\_ID}\_1.fastq
fastqc -o ./fastqc\_pretrim/ SRR09833${SLURM\_ARRAY\_TASK\_ID}\_2.fastq
```
*   %A in the #SBATCH line becomes the job ID
*   %a in the #SBATCH line becomes the array index
*   ```${SLURM_ARRAY_TASK_ID}``` is a shell variable that is set when the job runs, and it is substituted into the parameter to generate the proper filename

You submit your jobs with the command sbatch fastqc.sbatch. If the directory ```fastqc_pretrim``` does not exist, you may need to create one mkdir ```fastqc_pretrim```.

Non-sequentially named files
----------------------------

Job arrays are easy if the files are named sequentially in the example above. If they are not, you need to play some tricks. If for example you would like to run fastqc on all \*\_1.fastq files within the above folder, you can use a combination of ls, head and tail to get the name of the file for each task. Create your SLURM batch file run\_fastqc.sbatch.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J fastqc
#SBATCH --output=fastqc-%A\_%a.out
#SBATCH --array=1-6 # job array index
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.

module load FastQC/0.11.5

# get file name
file=\`ls \*\_1.fastq | head -n $SLURM\_ARRAY\_TASK\_ID | tail -n 1\`

echo "parsing sample: "$file

fastqc -o ./fastqc\_posttrim/ $file
```
### Bowtie2 examples

If you would like to use bowtie2 on samples within this directory, it is also possible to use job arrays. My solution is a little tricky, but manageable.

First, generate a file jobs for the tasks.
```commandline
ls *_1.fastq |cut -d_ -f1 > jobs
```
The contents of this file are:

```
SRR098333
SRR098334
SRR098335
SRR098336
SRR098337
SRR098338
```
Then, you can submit the following jobs array to HTC cluster.

```commandline
#!/bin/bash
#
#SBATCH --job-name=bowtie2
#SBATCH -N 1
#SBATCH --cpus-per-task=16 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bowtie2-%A\_%a.out
#SBATCH --array=0-5 # job array index

module load bowtie2/2.3.2-gcc5.2.0

names=($(cat jobs))

echo ${names\[${SLURM\_ARRAY\_TASK\_ID}\]}

bowtie2 -p 16 -x /bgfs/genomics/refs/GATK\_Resource\_Bundle/b37/human\_g1k\_v37.bowtie2\_index \\
  -1 ${names\[${SLURM\_ARRAY\_TASK\_ID}\]}\_1.fastq \\
  -2 ${names\[${SLURM\_ARRAY\_TASK\_ID}\]}\_2.fastq \\
  -S alignments/${names\[${SLURM\_ARRAY\_TASK\_ID}\]}.bowtie2.sam

${names\[${SLURM\_ARRAY\_TASK\_ID}\]} becomes each line within file jobs.
```
Using a "commands" file
-----------------------

The approach to use the job index works well for a single parameter, or a set of parameters that can be mapped to natural numbers (in this case, the different parameter would be calculated from the job index). There are also cases with multiple parameters that cannot be mapped to natural numbers. Then an alternative technique would be to create a text file "commands" which contains 1 command per line.

Then we can use the variable ```$SLURM_ARRAY_TASK_ID``` as a pointer determining which line of the file a job executes.

For example, we would like to run "bwa mem" on 10 samples with different RG tag. Generate a text file with one sample information per line.

```commandline
[fangping@login0b jobs]$ cat bwa_mem.txt
-Y -R "@RG\\tID:Exome\_Norm\\tPL:ILLUMINA\\tPU:C1TD1ACXX-CGATGT.7\\tLB:exome\_norm\_lib1\\tSM:HCC1395BL\_DNA" -o ../results/bwa/Exome\_Norm.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/Exome/Exome\_Norm\_R1.fastq.gz ../fastqs/Exome/Exome\_Norm\_R2.fastq.gz
-Y -R "@RG\\tID:Exome\_Tumor\\tPL:ILLUMINA\\tPU:C1TD1ACXX-ATCACG.7\\tLB:exome\_tumor\_lib1\\tSM:HCC1395\_DNA" -o ../results/bwa/Exome\_Tumor.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/Exome/Exome\_Tumor\_R1.fastq.gz ../fastqs/Exome/Exome\_Tumor\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Norm\_Lane1\\tPL:ILLUMINA\\tPU:D1VCPACXX.6\\tLB:wgs\_norm\_lib1\\tSM:HCC1395BL\_DNA" -o ../results/bwa/WGS\_Norm\_Lane1.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Norm\_Lane1\_R1.fastq.gz ../fastqs/WGS/WGS\_Norm\_Lane1\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Norm\_Lane2\\tPL:ILLUMINA\\tPU:D1VCPACXX.7\\tLB:wgs\_norm\_lib2\\tSM:HCC1395BL\_DNA" -o ../results/bwa/WGS\_Norm\_Lane2.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Norm\_Lane2\_R1.fastq.gz ../fastqs/WGS/WGS\_Norm\_Lane2\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Norm\_Lane3\\tPL:ILLUMINA\\tPU:D1VCPACXX.8\\tLB:wgs\_norm\_lib3\\tSM:HCC1395BL\_DNA" -o ../results/bwa/WGS\_Norm\_Lane3.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Norm\_Lane3\_R1.fastq.gz ../fastqs/WGS/WGS\_Norm\_Lane3\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Tumor\_Lane1\\tPL:ILLUMINA\\tPU:D1VCPACXX.1\\tLB:wgs\_tumor\_lib1\\tSM:HCC1395\_DNA" -o ../results/bwa/WGS\_Tumor\_Lane1.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Tumor\_Lane1\_R1.fastq.gz ../fastqs/WGS/WGS\_Tumor\_Lane1\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Tumor\_Lane2\\tPL:ILLUMINA\\tPU:D1VCPACXX.2\\tLB:wgs\_tumor\_lib1\\tSM:HCC1395\_DNA" -o ../results/bwa/WGS\_Tumor\_Lane2.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Tumor\_Lane2\_R1.fastq.gz ../fastqs/WGS/WGS\_Tumor\_Lane2\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Tumor\_Lane3\\tPL:ILLUMINA\\tPU:D1VCPACXX.3\\tLB:wgs\_tumor\_lib2\\tSM:HCC1395\_DNA" -o ../results/bwa/WGS\_Tumor\_Lane3.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Tumor\_Lane3\_R1.fastq.gz ../fastqs/WGS/WGS\_Tumor\_Lane3\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Tumor\_Lane4\\tPL:ILLUMINA\\tPU:D1VCPACXX.4\\tLB:wgs\_tumor\_lib2\\tSM:HCC1395\_DNA" -o ../results/bwa/WGS\_Tumor\_Lane4.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Tumor\_Lane4\_R1.fastq.gz ../fastqs/WGS/WGS\_Tumor\_Lane4\_R2.fastq.gz
-Y -R "@RG\\tID:WGS\_Tumor\_Lane5\\tPL:ILLUMINA\\tPU:D1VCPACXX.5\\tLB:wgs\_tumor\_lib3\\tSM:HCC1395\_DNA" -o ../results/bwa/WGS\_Tumor\_Lane5.sam ../results/reference\_genome/hg38/Homo\_sapiens\_assembly38.fasta ../fastqs/WGS/WGS\_Tumor\_Lane5\_R1.fastq.gz ../fastqs/WGS/WGS\_Tumor\_Lane5\_R2.fastq.gz
```
You can submit the following job arrays.

```commandline
[fangping@login0b jobs]$ cat 2.bwa.sbatch
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J bwa\_human\_samples
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
#SBATCH --mem=60g # Memory pool for all cores (see also --mem-per-cpu)
#SBATCH --array=1-10 # job array index
#SBATCH --output=bwa-%A_%a.out

module purge
module load gcc/8.2.0 bwa/0.7.17

mkdir -p ../results/bwa

arrayjob=`cat bwa_mem.txt | awk -v line=$SLURM_ARRAY_TASK_ID '{if (NR == line) print $0}'`

bwa\_command="bwa mem -t 8 "

# echo the command
echo $bwa_command$arrayjob

# run the command
bash -c "$bwa_command$arrayjob"
```
```awk``` is used to select the line ```$SLURM_ARRAY_TASK_ID``` from ```bwa_mem.txt``` file. ```bash -c``` is used to run the command.

Slurm wrap
----------

The wrap feature of sbatch can be used to submit multiple jobs at once.

From the man page for sbatch:
```commandline
--wrap=<command string>
```
Sbatch will wrap the specified command string in a simple "sh" shell script, and submit that script to the slurm controller. When --wrap is used, a script name and arguments may not be specified on the command line; instead the sbatch-generated wrapper script is used.

For example, lets say you want to run gzip on all fastq files within this directory. Create a shell script called ```run_gzip.sh```:

loop over all fastq files in the directory, print the filename and submit the gzip jobs to SLURM

```commandline
#!/bin/bash
for FILE in \*.fastq; do
    echo ${FILE}
    sbatch -n 1 -t 1-00:00 --wrap="gzip ${FILE}"
    sleep 1 # pause to be kind to the scheduler
done
```
then run script, which will submit a SLURM job for every .fastq file in the directory and gzip it.
```commandline
./run_gzip.sh
```
If you meet "permission denied" problem, you should change the file permission.
```commandline
chmod +x run_gzip.sh
```
Here we make a variable FILE that will match all files matching the string pattern ```*.fastq```. Then we toss that as an argument to sbatch.