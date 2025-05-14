# Variant Calling

## Nvidia parabricks

https://docs.nvidia.com/clara/parabricks/latest/overview.html
Parabricks is a free software suite for performing secondary analysis of next generation sequencing (NGS) DNA and RNA data. It delivers results at blazing fast speeds and low cost. Under the hood, Parabricks achieves this performance through tight integration with GPUs, which excel at performing data-parallel computation much more effectively than traditional CPU-based solutions. Parabricks was built from the ground up by GPU computing and Deep Learning experts who wanted to develop the fastest and most efficient possible implementation of common genomics algorithms used in secondary analysis.

You must have CRCD’s GPU cluster allocation to use parabricks. Three versions of parabricks have been installed.         
```commandline
parabricks/4.0.1
parabricks/4.2.1
parabricks/4.5.0
```

https://docs.nvidia.com/clara/parabricks/latest/tutorials/how-tos/somaticcalling.html

This how-to runs through a full Whole Genome Sequencing (WGS) somatic variant analysis pipeline for calling SNPs, MNPs, and small indels on real 30X short-read human data.
The demo is at /ix1/genomics/demo/illumina/gatk4/somatic_svc

```commandline
[fangping@login3 somatic_svc]$ cat fq2bam_4gpu_normal.sbatch
#!/bin/bash
#SBATCH --job-name=parabricks_l40s_job
#SBATCH --cluster=gpu
#SBATCH --partition=l40s
#SBATCH --nodes=1                # node count
#SBATCH --cpus-per-task=64        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --gres=gpu:4             # number of gpus per node
#SBATCH --time=3-00:00:00          # total run time limit (HH:MM:SS)
#SBATCH --error=fq2bam_normal.err
#SBATCH --output=fq2bam_normal.out

module purge
module load parabricks/4.2.1

mkdir -p results

DATAPATH=/ix1/genomics/demo/gatk4/somatic_svc

time pbrun fq2bam --num-gpus 4 \
    --ref $DATAPATH/ref/GRCh38.d1.vd1.fa \
    --in-fq $DATAPATH/SRR7890827_1.fastq.gz $DATAPATH/SRR7890827_2.fastq.gz "@RG\tID:id_SRR7890827_rg1\tLB:lib1\tPL:bar\tSM:sm_SRR7890827\tPU:pu_SRR7890827_rg1" \
    --knownSites $DATAPATH/Mills_and_1000G_gold_standard.indels.b38.primary_assembly.vcf.gz \
    --knownSites $DATAPATH/GCF_000001405.39.gz \
    --knownSites $DATAPATH/ALL.wgs.1000G_phase3.GRCh38.ncbi_remapper.20150424.shapeit2_indels.vcf.gz \
    --out-recal-file results/SRR7890827-WGS_FD_N_BQSR_REPORT.txt \
    --bwa-options=-Y \
    --out-bam results/SRR7890827-WGS_FD_N.bam \
    --tmp-dir=$SLURM_SCRATCH
```

You can submit the job fq2bam_4gpu_normal.sbatch and fq2bam_4gpu_tumor.sbatch to run fq2bam for normal and tumor samples.

```commandline
sbatch fq2bam_4gpu_normal.sbatch
sbatch fq2bam_4gpu_tumor.sbatch
```

After the two jobs are finished, you can run mutect2.

```commandline
[fangping@login3 somatic_svc]$ cat mutect2_4gpu.sbatch
#!/bin/bash
#SBATCH --job-name=parabricks_l40s_job
#SBATCH --cluster=gpu
#SBATCH --partition=l40s
#SBATCH --nodes=1                # node count
#SBATCH --cpus-per-task=64        # cpu-cores per task (>1 if multi-threaded tasks)
#SBATCH --gres=gpu:4             # number of gpus per node
#SBATCH --time=3-00:00:00          # total run time limit (HH:MM:SS)
#SBATCH --error=mutect2.err
#SBATCH --output=mutect2.out

module purge
module load parabricks/4.2.1

mkdir -p results

DATAPATH=/ix1/genomics/demo/gatk4/somatic_svc

time pbrun mutectcaller --num-gpus 4 \
    --ref $DATAPATH/ref/GRCh38.d1.vd1.fa \
    --in-tumor-bam $DATAPATH/results/SRR7890824-WGS_FD_T.bam \
    --in-normal-bam $DATAPATH/results/SRR7890827-WGS_FD_N.bam \
    --in-tumor-recal-file $DATAPATH/results/SRR7890824-WGS_FD_T_BQSR_REPORT.txt  \
    --in-normal-recal-file $DATAPATH/results/SRR7890827-WGS_FD_N_BQSR_REPORT.txt \
    --out-vcf results/SRR7890824-SRR7890827-WGS_FD.vcf \
    --tumor-name sm_SRR7890824 \
    --normal-name sm_SRR7890827 \
    --tmp-dir=$SLURM_SCRATCH
```

## Old Document

## Germline Variant Calling

SNV calling from NGS data refers to a range of methods for identifying the existence of single nucleotide variants 
(SNVs) from the results of high–throughput sequencing (HTS) experiments. Most HTS based methods for SNV detection are 
designed to detect germline variations in the individual's genome. These are the mutations that an individual 
biologically inherits from their parents, and are the usual type of variants searched for when performing such analysis 
(except for certain specific applications where somatic mutations are sought). Somatic variants correspond to mutations 
that have occurred de novo within groups of somatic cells within an individual (that is, they are not present within 
the individual's germline cells). This form of analysis has been frequently applied to the study of cancer, where many 
studies are designed around investigating the profile of somatic mutations within cancerous tissues.

Germline SNV calling are based around:

<ol>
	<li>Filtering the set of HTS reads to remove sources of error/bias</li>
	<li>Aligning the reads to a reference genome</li>
	<li>Using an algorithm, either based on a statistical model or some heuristics, to predict the likelihood of variation at each locus, based on the quality scores and allele counts of the aligned reads at that locus</li>
	<li>Filtering the predicted results, often based on metrics relevant to the application</li>
	<li>SNP annotation to predict the functional effect of each variation.</li>
</ol>

The usual output of these procedures is a VCF file.

### Human reference Data

The GATK resource bundle is a collection of standard files for working with human resequencing data with the GATK. 
GATK resource bundle is at /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle

#### GRCh37

Genome Reference Consortium Human Build 37

#### hg38

Genome Reference Consortium Human Build 38

#### hg19

Similar to GRCh37, this is the February 2009 assembly of the human genome with a different mitochondrial sequence 
and additional alternate haplotype assemblies.

#### b37

The reference genome included by some versions of the GATK software which includes data from GRCh37, the rCRS 
mitochondrial sequence, and the Human herpesvirus 4 type 1 in one file

Under b37 directory, you can find file named human_g1k_v37_decoy.fasta. This GRCh37-derived alignment set includes 
chromosomal plus unlocalized and unplaced contigs, the rCRS mitochondrial sequence (AC:NC_012920), Human herpesvirus 
4 type 1 (AC:NC_007605) and decoy sequence derived from HuRef, Human Bac and Fosmid clones and NA12878.

The big difference between the reference genome major releases is the coordinate system and the content. After you 
pick a genome, you should stick with it throughout your entire analysis to avoid issues. If you plan to use GATK for 
variant analysis, hg_g1k_v37 is the current recommended reference.

### Getting sample data

We will use GATK best practices to analyze 6 exome sequence data. The 6 samples are from 1000 Genomes CHB (Han Chinese 
Beijing) exome data, and their SRA records are SRR098333 ~ SRR098338.

Submit the following job to download a single SRA data.

```commandline
#!/bin/bash
#
#SBATCH --job-name=sratoolkit
#SBATCH -N 1
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=sratoolkit.out

module load SRAToolkit/2.9.1

prefetch SRR098333
fastq-dump --split-3 --qual-filter-1 --gzip SRR098333
```

<ul>
	<li>--split-3 Legacy 3-file splitting for mate-pairs. If the experiment is single-end sequencing, only one fastq file will be generated. If it is paired-end sequencing, there may be two or three fastq files. Two files (with suffix "_1" and "_2") are matched mate-pair read file whereas the third one (without any suffix) contains all the reads that do not have any mate-pairs (or SRA couldn't resolve mate-pairs for them).</li>
	<li>--qual-filter-1 Filter used in current 1000 Genomes data</li>
</ul>

You can use slurm job array to download the 6 SRA records.


```commandline
#!/bin/bash
#
#SBATCH --job-name=sratoolkit
#SBATCH -N 1
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=sratoolkit-%A_%a.out
#SBATCH --array=3-8 # job array index

module load SRAToolkit/2.9.1

echo SRR09833${SLURM_ARRAY_TASK_ID}

prefetch SRR09833${SLURM_ARRAY_TASK_ID}
fastq-dump --split-3 --qual-filter-1 --gzip SRR09833${SLURM_ARRAY_TASK_ID}
```

Note that SRAToolkit will download the raw sra files to your home directory ~/ncbi, which has limited storage space.

To set up your download/cache area for downloaded files and references., on HTC login node:

```commandline
module load SRAToolkit/2.9.1

vdb-config -i
```


### Aligning the reads to a reference genome

We can use bwa_mem or bowtie2 to align the reads to a reference genome. We choose b37 as the human reference genome.

To use bwa_mem to align the SRR098333 sample, submit the following jobs to HTC cluster.

```commandline
#!/bin/bash
#
#SBATCH --job-name=bwa_mem
#SBATCH -N 1
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bwa_mem.out
 
module load bwa/0.7.15-gcc5.2.0
 
#bwa index /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta
 
bwa mem -t 8 /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta SRR098333_1.fastq SRR098333_2.fastq > alignments/SRR098333.bwa_mem.sam

```


`bwa mem` needs a bwa index from a set of DNA sequences. For b37, this index has been built. Use codes in the comment 
line to build your own index.

You can use slurm job array to align the 6 samples to human reference genome.

```commandline
#!/bin/bash
#
#SBATCH --job-name=bwa_mem
#SBATCH -N 1
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bwa_mem-%A_%a.out
#SBATCH --array=3-8 # job array index

module load bwa/0.7.15-gcc5.2.0

echo SRR09833${SLURM_ARRAY_TASK_ID}

#bwa index /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta

bwa mem -t 4 /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta ./fastqs/SRR09833${SLURM_ARRAY_TASK_ID}_1.fastq.gz ./fastqs/SRR09833${SLURM_ARRAY_TASK_ID}_2.fastq.gz > alignments/SRR09833${SLURM_ARRAY_TASK_ID}.bwa_mem.sam
```


To use bowtie2 to align the sequences, submit the following jobs to HTC cluster.

```commandline
#!/bin/bash
#
#SBATCH --job-name=bowtie2
#SBATCH -N 1
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bowtie2-%A_%a.out
#SBATCH --array=3-8 # job array index

module load bowtie2/2.3.2-gcc5.2.0

echo SRR09833${SLURM_ARRAY_TASK_ID}

# bowtie2-build --threads 4 -f /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.bowtie2_index

bowtie2 -p 4 -x /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.bowtie2_index -1 ./fastqs/SRR09833${SLURM_ARRAY_TASK_ID}_1.fastq.gz -2 ./fastqs/SRR09833${SLURM_ARRAY_TASK_ID}_2.fastq.gz -S alignments/SRR09833${SLURM_ARRAY_TASK_ID}.bowtie2.sam
```

### Germline SNP &amp; Indel Discovery in Exome Sequence

Then I followed GATK's Best Practices for Germline SNP &amp; Indel Discovery in Whole Genome and Exome Sequence to 
analyze the data set.

#### Convert to BAM and sort

First, use samtools to sort by position and convert to bam format.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J samtools_sort_human_samples
#SBATCH --output=samtools.out
 
#SBATCH --cpus-per-task=16 # Request that ncpus be allocated per process.
 
module load samtools/1.3.1-gcc5.2.0
 
samtools sort -@ 16 -o SRR098333.bwa_mem.sorted.bam SRR098333.bwa_mem.sam
samtools sort -@ 16 -o SRR098334.bwa_mem.sorted.bam SRR098334.bwa_mem.sam
samtools sort -@ 16 -o SRR098335.bwa_mem.sorted.bam SRR098335.bwa_mem.sam
samtools sort -@ 16 -o SRR098336.bwa_mem.sorted.bam SRR098336.bwa_mem.sam
samtools sort -@ 16 -o SRR098337.bwa_mem.sorted.bam SRR098337.bwa_mem.sam
samtools sort -@ 16 -o SRR098338.bwa_mem.sorted.bam SRR098338.bwa_mem.sam
 
samtools sort -@ 16 -o SRR098333.bowtie2.sorted.bam SRR098333.bowtie2.sam
samtools sort -@ 16 -o SRR098334.bowtie2.sorted.bam SRR098334.bowtie2.sam
samtools sort -@ 16 -o SRR098335.bowtie2.sorted.bam SRR098335.bowtie2.sam
samtools sort -@ 16 -o SRR098336.bowtie2.sorted.bam SRR098336.bowtie2.sam
samtools sort -@ 16 -o SRR098337.bowtie2.sorted.bam SRR098337.bowtie2.sam
samtools sort -@ 16 -o SRR098338.bowtie2.sorted.bam SRR098338.bowtie2.sam
```

#### Identify read group information

The read group information is key for downstream GATK functionality. The GATK will not work without a read group tag. 
Make sure to enter as much metadata as you know about your data in the read group fields provided.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J picard_human_samples
# #SBATCH --output=samtools.out
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load picard/2.11.0
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098333.bwa_mem.sorted.bam O=SRR098333.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18634 RGPL=illumina RGPU=NA18634 RGSM=NA18634
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098334.bwa_mem.sorted.bam O=SRR098334.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18553 RGPL=illumina RGPU=NA18553 RGSM=NA18553
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098335.bwa_mem.sorted.bam O=SRR098335.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18631 RGPL=illumina RGPU=NA18631 RGSM=NA18631
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098336.bwa_mem.sorted.bam O=SRR098336.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18616 RGPL=illumina RGPU=NA18616 RGSM=NA18616
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098337.bwa_mem.sorted.bam O=SRR098337.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18638 RGPL=illumina RGPU=NA18638 RGSM=NA18638
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar AddOrReplaceReadGroups I=SRR098338.bwa_mem.sorted.bam O=SRR098338.bwa_mem.sorted_rg.bam SORT_ORDER=coordinate RGLB=NA18567 RGPL=illumina RGPU=NA18567 RGSM=NA18567
```

#### Mark duplicates

Once your data has been mapped to the reference genome, you can proceed to mark duplicates. The idea here is that 
during the sequencing process, the same DNA fragments may be sequenced several times. The resulting duplicate reads are 
not informative and should not be counted as additional evidence for or against a putative variant.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J picard_human_samples
# #SBATCH --output=picard.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load picard/2.11.0
 
echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar MarkDuplicates I=SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_rg.bam O=SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed.bam METRICS_FILE=SRR09833$SLURM_ARRAY_TASK_ID_metrics.txt CREATE_INDEX=TRUE
```

Here I used job arrays, which offer a mechanism for submitting and managing collections of similar jobs quickly and 
easily.

#### Perform local realignment of reads around indels

The local realignment process is designed to consume one or more BAM files and to locally realign reads such that the
number of mismatching bases is minimized across all the reads. Indel realignment is no longer necessary for variant 
discovery if you plan to use a variant caller that performs a haplotype assembly step, such as HaplotypeCaller or 
MuTect2.

There are 2 steps to the realignment process:

<ul>
	<li>Determining (small) suspicious intervals which are likely in need of realignment (see the RealignerTargetCreator tool)</li>
</ul>
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=RealignerTargetCreator.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms5g -Xmx32g  -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar -T RealignerTargetCreator -nt 4 -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta -I SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed.bam -o SRR09833$SLURM_ARRAY_TASK_ID.forIndelRealigner.intervals -known /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/1000G_phase1.indels.b37.vcf -known /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/Mills_and_1000G_gold_standard.indels.b37.vcf
```

<ul>
	<li>Running the realigner over those intervals (IndelRealigner)</li>
</ul>

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=IndelRealigner-%A_%a.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms5g -Xmx32g -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar \
    -T IndelRealigner \
    -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
    -I SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed.bam \
    -known /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/1000G_phase1.indels.b37.vcf \
    -known /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/Mills_and_1000G_gold_standard.indels.b37.vcf \
    -targetIntervals SRR09833$SLURM_ARRAY_TASK_ID.forIndelRealigner.intervals \
    -o SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed_indelrealigner.bam
```

Note the -know command, which provide known sites. Each GATK tool uses known sites differently, but what is common to 
all is that they use them to help distinguish true variants from false positives, which is very important to how these 
tools work. Check this [documentation](https://gatkforums.broadinstitute.org/gatk/discussion/1247/what-should-i-use-as-known-variants-sites-for-running-tool-x) 
for guidance to use as known variants/sites for running GATK tools.

#### Recalibrate Bases

Variant calling algorithms rely heavily on the quality scores assigned to the individual base calls in each sequence 
read. These scores are per-base estimates of error emitted by the sequencing machines. Unfortunately the scores 
produced by the machines are subject to various sources of systematic technical error, leading to over- or 
under-estimated base quality scores in the data. Base quality score recalibration (BQSR) is a process in which we 
apply machine learning to model these errors empirically and adjust the quality scores accordingly. This allows us to 
get more accurate base qualities, which in turn improves the accuracy of our variant calls.

The base recalibration process involves two key steps:

<ul>
	<li>first the program builds a model of covariation based on the data and a set of known variants (which you can bootstrap if there is none available for your organism)</li>
</ul>

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=BaseRecalibrator-%A_%a.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms5g -Xmx32g -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar \
    -T BaseRecalibrator \
    -nct 4 \
    -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
    -knownSites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/1000G_phase1.indels.b37.vcf \
    -knownSites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/Mills_and_1000G_gold_standard.indels.b37.vcf \
    -knownSites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/dbsnp_138.b37.vcf \
    -I SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed_indelrealigner.bam \
    -o SRR09833$SLURM_ARRAY_TASK_ID.recal_data.table
```

<ul>
	<li>then it adjusts the base quality scores in the data based on the model. This step is not necessary for some tools. These tools can directly use the above recal_data.table.</li>
</ul>
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --exclusive
#SBATCH --output=PrintReads-3.8.0_lowmem.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
# echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms1g -Xmx5g -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar -T PrintReads -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta -BQSR SRR098333.recal_data.table -I SRR098333.bwa_mem.sorted_dups_removed_indelrealigner.bam -o SRR098333.bwa_mem.sorted_dups_removed_indelrealigner_BQSR.bam
```

#### Variant Discovery

Once you have pre-processed your data according to our recommendations, you are ready to undertake the variant 
discovery process, i.e. identify the sites where your data displays variation relative to the reference genome, and 
calculate genotypes for each sample at that site.

For DNA, the variant calling step is further subdivided into two separate steps (per-sample calling followed by joint 
genotyping across samples) in order to enable scalable and incremental processing of cohorts comprising many individual 
samples.

<ul>
	<li>This workflow involves running HaplotypeCaller on each sample separately in GVCF mode, to produce an intermediate file format called GVCF (for Genomic VCF).</li>
</ul>

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=HaplotypeCaller-%A_%a.out
#SBATCH --array=3-8 # job array index 1, 2, ..., 16
 
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
echo "parsing sample: SRR09833" $SLURM_ARRAY_TASK_ID
 
java -Xms5g -Xmx32g -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar \
    -T HaplotypeCaller \
    -nct 4 \
    -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
    --emitRefConfidence GVCF \
    --dbsnp /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/dbsnp_138.b37.vcf \
    -I SRR09833$SLURM_ARRAY_TASK_ID.bwa_mem.sorted_dups_removed_indelrealigner.bam \
    -BQSR SRR09833$SLURM_ARRAY_TASK_ID.recal_data.table \
    -o SRR09833$SLURM_ARRAY_TASK_ID.raw.snps.indels.g.vcf
```

<ul>
	<li>The GVCFs of multiple samples are then run through a joint genotyping step to produce a multi-sample VCF callset, which can then be filtered to balance sensitivity and specificity as desired.</li>
</ul>


```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=GenotypeGVCFs.out
 
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/3.8.0
 
java -Xms5g -Xmx64g -jar /ihome/sam/apps/GATK/GenomeAnalysisTK-3.8.0/GenomeAnalysisTK.jar \
    -T GenotypeGVCFs \
    -nt 4 \
    -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
    -V SRR098333.raw.snps.indels.g.vcf \
    -V SRR098334.raw.snps.indels.g.vcf \
    -V SRR098335.raw.snps.indels.g.vcf \
    -V SRR098336.raw.snps.indels.g.vcf \
    -V SRR098337.raw.snps.indels.g.vcf \
    -V SRR098338.raw.snps.indels.g.vcf \
    -o SRR098333-8.GVCF.vcf
```

### Filter Variants

The GATK's variant calling tools are designed to be very lenient in order to achieve a high degree of sensitivity. 
This is good because it minimizes the chance of missing real variants, but it does mean that we need to filter the raw 
callset they produce in order to reduce the amount of false positives, which can be quite large.

<ul>
	<li>Recalibrate variant quality scores = run VQSR</li>
</ul>

VQSR requires at least 30 exome samples. Exmome data gives smaller number of variants per sample compared to WGS. A few 
exome samples are typically insufficient to build a robust recalibration model. If necessary, add exomes from 1000G 
Project or comparable.

The GVCF files for each alignment, along with 30 whole-exome sequencing samples from the 1,000 Genomes Project, were 
merged and joint-genotyped using GVCFs.

<ul>
	<li>Apply hard filters to a call set</li>
</ul>

#### Variant Annotation

The called variants can be further analyzed using VariantAnnotator and subjected to variant quality score recalibration 
of SNPs and INDELs separately. The variants that were called can be analyzed using ANNOVAR.

## DeepVariant

DeepVariant is an analysis pipeline that uses a deep neural network to call genetic variants from next-generation DNA 
sequencing data. In 2016 DeepVariant won PrecisionFDA Truth Challenge for best SNP Performance. DeepVariant maintains
high accuracy across data from different sequencing technologies, prep methods, and species.

Deepvariant is not easy to be installed. We have built singularity image for deepvariant.

DeepVariant consists of 3 main binaries: make_examples, call_variants, and postprocess_variants. In the 0.8 release,
one convenient command is introduced, and the command will run through all 3 steps that are required to go from a 
BAM file to the VCF/gVCF output files.

Submit the following job to download WES test data.

```commandline
#!/bin/bash
#
#SBATCH --job-name=download
#SBATCH -N 1
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM

wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/151002_7001448_0359_AC7F6GANXX_Sample_HG002-EEogPU_v02-KIT-Av5_AGATGTAC_L008.posiSrt.markDup.bai
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/151002_7001448_0359_AC7F6GANXX_Sample_HG002-EEogPU_v02-KIT-Av5_AGATGTAC_L008.posiSrt.markDup.bam
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/HG002_GRCh37_GIAB_highconf_CG-IllFB-IllGATKHC-Ion-10X-SOLID_CHROM1-22_v.3.3.2_highconf_noinconsistent.bed
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/HG002_GRCh37_GIAB_highconf_CG-IllFB-IllGATKHC-Ion-10X-SOLID_CHROM1-22_v.3.3.2_highconf_triophased.vcf.gz
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/HG002_GRCh37_GIAB_highconf_CG-IllFB-IllGATKHC-Ion-10X-SOLID_CHROM1-22_v.3.3.2_highconf_triophased.vcf.gz.tbi
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/agilent_sureselect_human_all_exon_v5_b37_targets.bed
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/hs37d5.fa.gz
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/hs37d5.fa.gz.fai
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/hs37d5.fa.gz.gzi
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/hs37d5.fa.gzi
wget https://storage.googleapis.com/deepvariant/exome-case-study-testdata/hs37d5.fa.fai
```

Submit the following job to run DeepVariant with one command on WES test data.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM

#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.

module load deepvariant/0.8.0

singularity -s exec -B /bgfs:/bgfs  \
  /ihome/crc/install/deepvariant/deepvariant.0.8.0.simg \
  /opt/deepvariant/bin/run_deepvariant \
  --model_type=WES \
  --ref=hs37d5.fa.gz \
  --reads=151002_7001448_0359_AC7F6GANXX_Sample_HG002-EEogPU_v02-KIT-Av5_AGATGTAC_L008.posiSrt.markDup.bam \
  --regions=agilent_sureselect_human_all_exon_v5_b37_targets.bed \
  --output_vcf=output.vcf.gz \
  --output_gvcf=output.g.vcf.gz \
  --num_shards=8
```

`-B /bgfs:/bgfs` When Singularity ‘binds’ the host operating system to the one inside your container, the host file 
systems becomes inaccessible. But you may want to read and write files on the host system from within the container. 
-B command-line option to specify bind paths.

Submit the following job to download WGS test data.

```commandline
#!/bin/bash
#
#SBATCH --job-name=download
#SBATCH -N 1
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM

wget https://storage.googleapis.com/deepvariant/case-study-testdata/HG002_NIST_150bp_50x.bam
wget https://storage.googleapis.com/deepvariant/case-study-testdata/HG002_NIST_150bp_50x.bam.bai
```

Submit the following job to run DeepVariant with one command on WGS test data.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM

#SBATCH --cpus-per-task=16 # Request that ncpus be allocated per process.

module load deepvariant/0.8.0

singularity -s exec -B /bgfs:/bgfs,tmp:/tmp  \
  /ihome/crc/install/deepvariant/deepvariant.0.8.0.simg \
  /opt/deepvariant/bin/run_deepvariant \
  --model_type=WGS \
  --ref=hs37d5.fa.gz \
  --reads=HG002_NIST_150bp_50x.bam \
  --output_vcf=output_wgs.vcf.gz \
  --output_gvcf=output_wgs.g.vcf.gz \
  --num_shards=16
```

Note that the default TMPDIR uses /tmp on computational node. The size of this folder is not sufficient for WGS data. 
I have created a tmp folder under current directory. "-B tmp:/tmp" binds the tmp folder to /tmp in singularity image. 
Make sure that your current folder have enough space to store deepvariant_tmp_output.

## Somatic Variant Calling

Detection of cancer mutations is different from the detection of germline genetic variants, mainly because:

<ul>
	<li>We are only interested in somatic mutations which are the differences between tumor and germline DNA.</li>
	<li>Tumor samples are often impure due to a mixture of tumor and normal cells.</li>
	<li>Tumors consists of sub-clones with different somatic mutations.</li>
</ul>

Germline genotype callers such as GATK's HaplotypeCaller are optimized for diploid samples or samples of known ploidy, 
and for detecting variants with allele frequencies close to 0.5 or 1. Therefore, somatic variants should be called with 
specialized callers. We follow GATK best practices for somatic mutation using Exome/Panel + Whole Genome data. MuTect2 
is a somatic SNP and indel caller that combines the DREAM challenge-winning somatic genotyping engine of the original 
MuTect with the assembly-based machinery of HaplotypeCaller.

### Data

As part of its goal to overcome obstacles in realizing the full benefits of the application of genomic methods for 
cancer diagnosis and the guidance of precision treatment, the BCM Human Genome Sequencing Center and the TCRB has made 
genomic sequence data from high quality human tumor specimens with matched normal freely available to the public without
the barriers that other, more restrictive data sharing initiatives impose. This data set can be accessed at 
<a href="http://txcrb.org/open.html" title="http://txcrb.org/open.html">http://txcrb.org/open.html</a>

We have applied mutect2 to the case_001, case_002 and case_003 tumor-normal pair data. For case_001, 
download TCRBOA1-N-WEX.read1.fastq.bz2, TCRBOA1-N-WEX.read2.fastq.bz2, TCRBOA1-T-WEX.read1.fastq.bz2 and 
TCRBOA1-T-WEX.read1.fastq.bz2. Download the corresponding data for case_002 and case_003.

### Initial QC and adapter trimming

You should perform proper Initial QC and adapter trimming for your own data.

### Generation and initial processing of bam files

Bam files should be generated and preprocessed according to 
[GATKs Best Practice for variant discovery](https://www.broadinstitute.org/gatk/guide/best-practices).

#### Aligning the reads to a reference genome

We can use `bwa_mem` to align the reads to a reference genome. We choose b37 as the human reference genome.

To use `bwa_mem` to align the case_001 reads, submit the following jobs to HTC cluster.
 
```commandline
#!/bin/bash
#
#SBATCH --job-name=bwa_mem_case1
#SBATCH -N 1
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bwa_mem_case1.out
 
module load bwa/0.7.15-gcc5.2.0
module load samtools/1.5-gcc5.2.0
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:700807_20131106_C2P0JACXX-6-ID04\tLB:TCRB.TCR002103-B-1_1AMP\tDT:2013-11-17T20:22:39-0600\tSM:TCR002103-B\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_001/TCRBOA1-N-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_001/TCRBOA1-N-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA1-N-WEX.bam -
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:700807_20131106_C2P0JACXX-6-ID06\tLB:TCRB.TCR002103-T-1_1AMP\tDT:2013-11-17T20:25:26-0600\tSM:TCR002103-T\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_001/TCRBOA1-T-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_001/TCRBOA1-T-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA1-T-WEX.bam -
```

To align case_002 reads:
 
```commandline
#!/bin/bash
#
#SBATCH --job-name=bwa_mem_case2
#SBATCH -N 1
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bwa_mem_case2.out
 
module load bwa/0.7.15-gcc5.2.0
module load samtools/1.5-gcc5.2.0
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:700807_20131106_C2P0JACXX-4-ID05\tLB:TCRB.TCR002101-B-1_1AMP\tDT:2013-11-17T20:35:41-0600\tSM:TCR002101-B\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_002/TCRBOA2-N-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_002/TCRBOA2-N-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA2-N-WEX.bam -
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:700807_20131106_C2P0JACXX-4-ID07\tLB:TCRB.TCR002101-T-1_1AMP\tDT:2013-11-17T20:39:59-0600\tSM:TCR002101-T\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_002/TCRBOA2-T-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_002/TCRBOA2-T-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA2-T-WEX.bam -
```

To align case_003 reads:

```commandline
#!/bin/bash
#
#SBATCH --job-name=bwa_mem_case3
#SBATCH -N 1
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=bwa_mem_case3.out
 
module load bwa/0.7.15-gcc5.2.0
module load samtools/1.5-gcc5.2.0
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:D00222_20131112_C2MTPACXX-3-ID10\tLB:TCRB.TCR002182-N-1_1TAG\tDT:2013-11-24T03:33:43-0600\tSM:TCR002182-N\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_003/TCRBOA3-N-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_003/TCRBOA3-N-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA3-N-WEX.bam -
 
bwa mem -t 8 -T 0 \
-R '@RG\tID:0\tPL:Illumina\tPU:D00222_20131112_C2MTPACXX-3-ID11\tLB:TCRB.TCR002182-T-1_1TAG\tDT:2013-11-24T03:30:59-0600\tSM:TCR002182-T\tCN:BCM' \
/mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
<(bzip2 -dc ../case_003/TCRBOA3-T-WEX.read1.fastq.bz2) \
<(bzip2 -dc ../case_003/TCRBOA3-T-WEX.read2.fastq.bz2) | samtools view -Shb -o TCRBOA3-T-WEX.bam -
```

#### Sort BAM and mark duplicates

Create a text file named jobs with the following content.

```commandline
TCRBOA1-N-WEX
TCRBOA1-T-WEX
TCRBOA2-N-WEX
TCRBOA2-T-WEX
TCRBOA3-N-WEX
TCRBOA3-T-WEX
```

Submit the following job arrays to HTC cluster to sort BAM files.
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J picard_human_samples
#SBATCH --output=picard_sort-%A_%a.out
#SBATCH --array=0-5 # job array index
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load picard/2.11.0
 
names=($(cat jobs))
 
echo ${names[${SLURM_ARRAY_TASK_ID}]}
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar SortSam \
CREATE_INDEX=true \
INPUT=${names[${SLURM_ARRAY_TASK_ID}]}.bam \
OUTPUT=${names[${SLURM_ARRAY_TASK_ID}]}_sorted.bam \
SORT_ORDER=coordinate \
VALIDATION_STRINGENCY=STRICT
```

Submit the following job arrays to HTC cluster to mark duplicates.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J picard_human_samples
#SBATCH --output=picard_md-%A_%a.out
#SBATCH --array=0-5 # job array index
 
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load picard/2.11.0
 
names=($(cat jobs))
 
echo ${names[${SLURM_ARRAY_TASK_ID}]}
 
java -Xms5g -Xmx16g -jar /ihome/sam/apps/picard/picard-2.11.0/picard.jar MarkDuplicates \
CREATE_INDEX=true \
INPUT=${names[${SLURM_ARRAY_TASK_ID}]}_sorted.bam \
OUTPUT=${names[${SLURM_ARRAY_TASK_ID}]}_sorted_md.bam \
VALIDATION_STRINGENCY=STRICT \
M=${names[${SLURM_ARRAY_TASK_ID}]}_marked_dup_metrics.txt
```

#### Recalibrate Bases

GATK4 enables selected tools to be run in a massively parallel way on local clusters using Apache Spark. The tools to 
perform recalibrate bases is named BQSRPipelineSpark. The following spark job template can be used to run GATK4 spark 
tools on HTC cluster.
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --exclusive # By default, spark will use the whole node.
#SBATCH --output=BQSRPipelineSpark-%A_%a.out
#SBATCH --array=0-5 # job array index 1, 2, ..., 16
 
module load GATK/4.0.0.0
 
names=($(cat jobs))
 
echo ${names[${SLURM_ARRAY_TASK_ID}]}
 
gatk BQSRPipelineSpark \
    -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.2bit \
    --known-sites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/1000G_phase1.indels.b37.vcf \
    --known-sites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/Mills_and_1000G_gold_standard.indels.b37.vcf \
    --known-sites /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/dbsnp_138.b37.vcf \
    -I ${names[${SLURM_ARRAY_TASK_ID}]}_sorted_md.bam \
    -O ${names[${SLURM_ARRAY_TASK_ID}]}_sorted_md_BQSR.bam \
    --conf "spark.local.dir=$SLURM_SCRATCH"
```

By default, spark jobs will use the whole node. Use --exclusive option to request a whole node. GATK4 spark tools 
require reference genome to be in 2bit format. Spark also generate large amounts of temporary files. I used local 
scratch space on each HTC node for these temporary files --conf "spark.local.dir=$LOCAL"

### Call somatic variants in Tumor with matched Normal using Mutect2

Mutect2 rejects from consideration any sites that are most likely to be germline or artifacts based on the paired 
Normal, a germline population resource and a Panel of Normals (PoN).

#### A panel of normals (PoN)

To make your own PoN, run Mutect2 in "tumoronly mode" on each normal BAM individually then run 
CreateSomaticPanelOfNormals on the resulting VCFs. To generate PoN using the three normal samples, submit the 
following job to HTC cluster.
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=PoN.out
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
module load samtools/1.5-gcc5.2.0
 
samtools index -@ 4 TCRBOA1-N-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA1-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002103-B \
   --native-pair-hmm-threads 4 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA1-N-WEX_for_pon.vcf.gz
 
samtools index -@ 4 TCRBOA2-N-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA2-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002101-B \
   --native-pair-hmm-threads 4 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA2-N-WEX_for_pon.vcf.gz
 
samtools index -@ 4 TCRBOA3-N-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA3-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002182-N \
   --native-pair-hmm-threads 4 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA3-N-WEX_for_pon.vcf.gz
```

then this job to CreateSomaticPanelOfNormals.
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=CreateSomaticPanelOfNormals.out
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
gatk CreateSomaticPanelOfNormals \
   -vcfs TCRBOA1-N-WEX_for_pon.vcf.gz \
   -vcfs TCRBOA2-N-WEX_for_pon.vcf.gz \
   -vcfs TCRBOA3-N-WEX_for_pon.vcf.gz \
   -O pon.vcf.gz
```

#### Use Mutect2 to Call somatic variants

To call somatic variants for case_001, submit the following job to HTC cluster.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=Mutect2_TCRBOA1.out
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
module load samtools/1.5-gcc5.2.0
 
samtools index -@ 8 TCRBOA1-T-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA1-T-WEX_sorted_md_BQSR.bam \
   -I TCRBOA1-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002103-T \
   -normal TCR002103-B \
   -pon pon.vcf.gz \
   --native-pair-hmm-threads 8 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA1_tumor_matched_m2_snvs_indels.vcf.gz \
   -bamout TCRBOA1_tn_out.bam
```

For case_002:

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=Mutect2_TCRBOA2.out
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
module load samtools/1.5-gcc5.2.0
 
samtools index -@ 8 TCRBOA2-T-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA2-T-WEX_sorted_md_BQSR.bam \
   -I TCRBOA2-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002101-T \
   -normal TCR002101-B \
   -pon pon.vcf.gz \
   --native-pair-hmm-threads 8 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA2_tumor_matched_m2_snvs_indels.vcf.gz \
   -bamout TCRBOA2_tn_out.bam
```

For case_003:
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=Mutect2_TCRBOA3.out
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
module load samtools/1.5-gcc5.2.0
 
samtools index -@ 8 TCRBOA3-T-WEX_sorted_md_BQSR.bam
 
gatk Mutect2 \
   -R /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/b37/human_g1k_v37.fasta \
   -I TCRBOA3-T-WEX_sorted_md_BQSR.bam \
   -I TCRBOA3-N-WEX_sorted_md_BQSR.bam \
   -tumor TCR002182-T \
   -normal TCR002182-N \
   -pon pon.vcf.gz \
   --native-pair-hmm-threads 8 \
   --germline-resource /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/Mutect2/af-only-gnomad.raw.sites.b37.vcf.gz \
   -O TCRBOA3_tumor_matched_m2_snvs_indels.vcf.gz \
   -bamout TCRBOA3_tn_out.bam
```

### Filter for confident calls

We have generated an unfiltered Mutect2 callset with annotations. Now, we will use filtering tools to identify which 
variant candidates we think we can trust to be real somatic variants.

We use a known germline variant resource to limit analyses to sites that are commonly variant. GetPileupSummaries 
checks the given sample at these sites and uses those that it determines are homozygous variant and uses contaminating 
alleles towards estimating contamination.

Submit the following job to HTC cluster to filter case_001 calls.
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=TCRBOA1_Filter.out
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
gatk GetPileupSummaries \
   -I TCRBOA1-T-WEX_sorted_md_BQSR.bam \
   -V /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/GetPileupSummaries/small_exac_common_3_b37.vcf.gz \
   -O TCRBOA1_getpileupsummaries.table
 
gatk CalculateContamination \
   -I TCRBOA1_getpileupsummaries.table \
   -O TCRBOA1_calculatecontamination.table
 
gatk FilterMutectCalls \
   -V TCRBOA1_tumor_matched_m2_snvs_indels.vcf.gz \
   --contamination-table TCRBOA1_calculatecontamination.table \
   -O TCRBOA1_somatic_filtered.vcf
```

For case_002:
 
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=TCRBOA2_Filter.out
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
gatk GetPileupSummaries \
   -I TCRBOA2-T-WEX_sorted_md_BQSR.bam \
   -V /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/GetPileupSummaries/small_exac_common_3_b37.vcf.gz \
   -O TCRBOA2_getpileupsummaries.table
 
gatk CalculateContamination \
   -I TCRBOA2_getpileupsummaries.table \
   -O TCRBOA2_calculatecontamination.table
 
gatk FilterMutectCalls \
   -V TCRBOA2_tumor_matched_m2_snvs_indels.vcf.gz \
   --contamination-table TCRBOA2_calculatecontamination.table \
   -O TCRBOA2_somatic_filtered.vcf
```

For case_003:

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH -J GATK_human_samples
#SBATCH --output=TCRBOA3_Filter.out
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.
 
module load GATK/4.0.0.0
 
gatk GetPileupSummaries \
   -I TCRBOA3-T-WEX_sorted_md_BQSR.bam \
   -V /mnt/mobydisk/pan/genomics/refs/GATK_Resource_Bundle/beta/GetPileupSummaries/small_exac_common_3_b37.vcf.gz \
   -O TCRBOA3_getpileupsummaries.table
 
gatk CalculateContamination \
   -I TCRBOA3_getpileupsummaries.table \
   -O TCRBOA3_calculatecontamination.table
 
gatk FilterMutectCalls \
   -V TCRBOA3_tumor_matched_m2_snvs_indels.vcf.gz \
   --contamination-table TCRBOA3_calculatecontamination.table \
   -O TCRBOA3_somatic_filtered.vcf
```

## RNA-seq variant analysis

RNA fusion detection. Another important application of RNA-seq is to detect fusion genes, which are abnormal genes 
produced by the concatenation of two separate genes arising from chromosomal translocations, or tran-splicing events. 
Fusion genes play a critical role in investigating causes and development of various cancer types. Based on recent 
publications (pubmed: 28680106), FusionCatcher yielded most sensitive and precise predictions.

This is an example of finding fusion genes in the BT474 cell line using the public available RNA-seq data 
(from SRA archive).

```commandline
mkdir Fusioncatcher_Test
cd Fusioncatcher_Test
 
wget https://ftp-private.ncbi.nlm.nih.gov/sra/sra-instant/reads/ByRun/sra/SRR/SRR064/SRR064438/SRR064438.sra
```

Submit the following batch job to HTC cluster.

```commandline
#!/bin/bash
#SBATCH -N 1
#SBATCH --cpus-per-task=4 # Request that ncpus be allocated per process.
#SBATCH -J Fusioncatcher_human_sample
#SBATCH -o Fusioncatcher.out
#SBATCH -t 24:00:00
 
module load fusioncatcher/0.99.7b
 
fusioncatcher -p 4 -i ./Fusioncatcher_samples/ -o ./results/
 
# fusioncatcher -d /ihome/sam/apps/fusioncatcher/fusioncatcher/data/ensembl_v86/ -i ./Fusioncatcher_Test/ -o ./results/
```

Options specified as follows:

<ul>
	<li>'-p PROCESSES', or ' --threads=PROCESSES' Number of processes/threads to be used for running SORT, Bowtie, BLAT, STAR, BOWTIE2 and other tools/programs. If this parameter is not specified, 1 core is used.</li>
	<li>' --config=CONFIGURATION_FILENAME', The default configuration file is at /ihome/sam/apps/fusioncatcher/fusioncatcher/etc/configuration.cfg</li>
	<li>'-i INPUT_FILENAME' The input file(s) or directory.</li>
	<li>'-o OUTPUT_DIRECTORY', The output directory where all the output files containing information about the found candidate fusiongenes are written.</li>
	<li>'-d DATA_DIRECTORY', The data directory where all the annotations files from Ensembl database are placed. This directory should be built using 'fusioncatcher-build'. The default directory is /ihome/sam/apps/fusioncatcher/fusioncatcher/data/ensembl_v86/</li>
</ul>
