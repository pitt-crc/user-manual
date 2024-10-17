# Cromwell/WDL and ENCODE Pipelines

https://crc.pitt.edu/content/encode-chip-seq-pipeline-2
https://crc.pitt.edu/content/encode-atac-seq-pipeline

## Introduction

The Workflow Description Language, or WDL (pronounced “widdle”), is a technical language developed at the Broad 
Institute that allows workflow developers to describe pipelines in a hardware-agnostic way — separating logic from 
the metal on which it runs. A WDL workflow is composed of a sequence of Tasks, each having its own inputs, outputs,
runtime environment, and commands. The runtime block outlines the shape of the virtual machine image in which the 
list of commands will run, producing outputs from inputs. Note that each WDL Task prescribes a custom runtime 
environment, which lends to very lean utilization of computational resources.

Cromwell is a workflow execution engine that can run WDL workflows, locally or in the cloud.

Caper (Cromwell Assisted Pipeline ExecutoR) is a wrapper Python package for Cromwell. Caper supports multiple platforms.

```commandline
# run the following command on HTC login node to generate caper configuration files under ~/.caper
module load caper/2.2.2
caper init slurm
```

This will create a default Caper configuration file ~/.caper/default.conf, which have only required parameters for 
slurm, the platform for the HTC cluster. This will also install Cromwell/Womtool JARs on ~/.caper. 
Downloading those files can take several minutes. Once they are installed, Caper can completely work offline with 
local data files.


## ENCODE Transcription Factor and Histone ChIP-Seq processing pipeline

### Introduction

This ChIP-Seq pipeline is based off the ENCODE (phase-3) transcription factor and histone ChIP-seq pipeline 
specifications (by Anshul Kundaje) in [this google doc](https://docs.google.com/document/d/1lG_Rd7fnYgRpSIqrIfuVlAz2dW1VaSQThzk836Db99c/edit).

### Features

    Portability: The pipeline run can be performed across different cloud platforms such as Google, AWS and DNAnexus, as well as on cluster engines such as SLURM, SGE and PBS.
    User-friendly HTML report: In addition to the standard outputs, the pipeline generates an HTML report that consists of a tabular representation of quality metrics including alignment/peak statistics and FRiP along with many useful plots (IDR/cross-correlation measures). An example of the [HTML report](https://storage.googleapis.com/encode-pipeline-test-samples/encode-chip-seq-pipeline/ENCSR000DYI/example_output/qc.html). The [json file](https://storage.googleapis.com/encode-pipeline-test-samples/encode-chip-seq-pipeline/ENCSR000DYI/example_output/qc.json) used in generating this report.
    Supported genomes: Pipeline needs genome specific data such as aligner indices, chromosome sizes file and blacklist. We provide a genome database downloader/builder for hg38, hg19, mm10, mm9. You can also use this [builder](https://github.com/ENCODE-DCC/chip-seq-pipeline2/blob/master/docs/build_genome_database.md) to build genome database from FASTA for your custom genome.


Example scripts are at /bgfs/genomics/fangping/encode/encode-chip-seq-pipeline

Genome databases are downloaded at /bgfs/genomics/refs/encode-pipeline-genome-data/encode-chip-seq-pipeline

### Input JSON file

An input JSON file specifies all the input parameters and files that are necessary for successfully running this pipeline. This includes a specification of the path to the genome reference files and the raw data fastq file. Please make sure to specify absolute paths rather than relative paths in your input JSON files.

Example input JSON file: ENCSR000DYI.json

```json
{
    "chip.pipeline_type" : "tf",
    "chip.genome_tsv" : "/bgfs/genomics/refs/encode-pipeline-genome-data/encode-chip-seq-pipeline/hg38.tsv",
    "chip.fastqs_rep1_R1" : ["/bgfs/genomics/fangping/encode/encode_chipseq/ENCSR000DYI/rep1.fastq.gz"],
    "chip.fastqs_rep2_R1" : ["/bgfs/genomics/fangping/encode/encode_chipseq/ENCSR000DYI/rep2.fastq.gz"],
    "chip.ctl_fastqs_rep1_R1" : ["/bgfs/genomics/fangping/encode/encode_chipseq/ENCSR000DYI/ctl1.fastq.gz"],
    "chip.ctl_fastqs_rep2_R1" : ["/bgfs/genomics/fangping/encode/encode_chipseq/ENCSR000DYI/ctl2.fastq.gz"],
    "chip.paired_end" : false,
    "chip.always_use_pooled_ctl" : true,
    "chip.title" : "ENCSR000DYI (m0, q30)",
    "chip.description" : "CEBPB ChIP-seq on human A549 produced by the Snyder lab",

    "chip.align.multimapping": 0,
    "chip.align_R1.multimapping": 0,
    "chip.align_ctl.multimapping": 0,
    "chip.mapq_thresh": 30,
    "chip.align_only": false
}
```

Submit the following caper leader job to the HTC cluster.

```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH --job-name=chipseq_caper_leader_job
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.

# This is the Caper leader job. A Caper leader job will sbatch lots of sub-tasks to SLURM

module load caper/2.2.2
module load singularity/3.9.6

caper run /ihome/crc/install/encode-dcc/chip-seq-pipeline2-2.2.0/chip.wdl --soft-glob-output \
 -i ENCSR000DYI.json \
 --singularity /ihome/crc/install/encode-dcc/chip-seq-pipeline2-v2.2.0.sif
```

Options specified as follows:

    Pipeline wdl specification is at /ihome/crc/install/encode-dcc/chip-seq-pipeline2-2.2.0/chip.wdl
    --soft-glob-output you must use this option because CRC NFS file system does not support hard link
    The software tools for this pipeline are installed as a singularity image at /ihome/crc/install/encode-dcc/chip-seq-pipeline2-v2.2.0.sif
    This caper leader jobs will submit many Cromwell jobs to the slurm queue.

Check the slurm output file. The workflow runs successfully when you see the following line at the end of slurm output file.

“This workflow ran successfully. There is nothing to troubleshoot”

### Use croo to organize outputs

Find a metadata.json on Caper's output directory chip.

```commandline
cd chip/965fdabc-b84d-4bd6-95f0-2057f325ee24
module load caper/2.2.2
croo metadata.json
```

## ENCODE ATAC-seq Pipeline

### Introduction

This pipeline is designed for automated end-to-end quality control and processing of ATAC-seq and DNase-seq data. 
The pipeline can be run on compute clusters with job submission engines as well as on stand alone machines. 
It inherently makes uses of parallelized/distributed computing. Pipeline installation is also easy as most dependencies 
are automatically installed. The pipeline can be run end-to-end, starting from raw FASTQ files all the way to peak 
calling and signal track generation using a single caper submit command. One can also start the pipeline from 
intermediate stages (for example, using alignment files as input). The pipeline supports both single-end and paired-end 
data as well as replicated or non-replicated datasets. The outputs produced by the pipeline include 1) formatted HTML 
reports that include quality control measures specifically designed for ATAC-seq and DNase-seq data, 2) analysis of reproducibility, 3) stringent and relaxed thresholding of peaks, 4) fold-enrichment and pvalue signal tracks. The pipeline also supports detailed error reporting and allows for easy resumption of interrupted runs. It has been tested on some human, mouse and yeast ATAC-seq datasets as well as on human and mouse DNase-seq datasets.

The ATAC-seq pipeline protocol specification is [here](https://docs.google.com/document/d/1f0Cm4vRyDQDu0bMehHD7P7KOMxTOP-HiNoIvL1VcBt8/edit?usp=sharing). Some parts of the ATAC-seq pipeline were developed in collaboration with Jason Buenrostro, Alicia Schep and Will Greenleaf at Stanford.
Features

    Portability: The pipeline run can be performed across different cloud platforms such as Google, AWS and DNAnexus, as well as on cluster engines such as SLURM, SGE and PBS.
    User-friendly HTML report: In addition to the standard outputs, the pipeline generates an HTML report that consists of a tabular representation of quality metrics including alignment/peak statistics and FRiP along with many useful plots (IDR/TSS enrichment). An example of the [HTML report](https://storage.googleapis.com/encode-pipeline-test-samples/encode-atac-seq-pipeline/ENCSR889WQX/example_output/qc.html). The [json file](https://storage.googleapis.com/encode-pipeline-test-samples/encode-atac-seq-pipeline/ENCSR889WQX/example_output/qc.json) used in generating this report.
    Supported genomes: Pipeline needs genome specific data such as aligner indices, chromosome sizes file and blacklist. We provide a genome database downloader/builder for hg38, hg19, mm10, mm9. You can also use this [builder](https://github.com/ENCODE-DCC/atac-seq-pipeline/blob/master/docs/build_genome_database.md) to build genome database from FASTA for your custom genome.

Example scripts are at /bgfs/genomics/fangping/encode/encode_atacseq

Genome databases are downloaded at /bgfs/genomics/refs/encode-pipeline-genome-data/encode-atac-seq-pipeline

### Input JSON file

An input JSON file specifies all the input parameters and files that are necessary for successfully running this pipeline. This includes a specification of the path to the genome reference files and the raw data fastq file. Please make sure to specify absolute paths rather than relative paths in your input JSON files.

Example input JSON file: samples.json
```json
{
    "atac.pipeline_type" : "atac",
    "atac.genome_tsv" : "/bgfs/genomics/refs/encode-pipeline-genome-data/encode-atac-seq-pipeline/hg38.tsv",
    "atac.fastqs_rep1_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601846_1.fastq.gz" ],
    "atac.fastqs_rep1_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601846_2.fastq.gz" ],
    "atac.fastqs_rep2_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601847_1.fastq.gz" ],
    "atac.fastqs_rep2_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601847_2.fastq.gz" ],
    "atac.fastqs_rep3_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601848_1.fastq.gz" ],
    "atac.fastqs_rep3_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601848_2.fastq.gz" ],
    "atac.fastqs_rep4_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601857_1.fastq.gz" ],
    "atac.fastqs_rep4_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601857_2.fastq.gz" ],
    "atac.fastqs_rep5_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601858_1.fastq.gz" ],
    "atac.fastqs_rep5_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601858_2.fastq.gz" ],
    "atac.fastqs_rep6_R1" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601859_1.fastq.gz" ],
    "atac.fastqs_rep6_R2" : [ "/bgfs/genomics/workshops/2020s/ATACSeq_data_analysis/macular/fastqs/SRR5601859_2.fastq.gz" ],
    "atac.paired_end" : true,
    "atac.auto_detect_adapter" : true,
    "atac.title" : "ATACSeq_workshop",
    "atac.description" : "macular"
}
```

Submit the following caper leader job to the HTC cluster.
```commandline
#!/bin/bash
#
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 6-00:00 # Runtime in D-HH:MM
#SBATCH --job-name=atacseq_caper_leader_job
#SBATCH --cpus-per-task=1 # Request that ncpus be allocated per process.

# This is the Caper leader job. A Caper leader job will sbatch lots of sub-tasks to SLURM

module load caper/2.2.2
module load singularity/3.9.6

caper run /ihome/crc/install/encode-dcc/atac-seq-pipeline-2.2.0/atac.wdl --soft-glob-output \
 -i samples.json \
 --singularity /ihome/crc/install/encode-dcc/atac-seq-pipeline-v2.2.0.sif
```

Options specified as follows:

    Pipeline wdl specification is at /ihome/crc/install/encode-dcc/atac-seq-pipeline-2.2.0/atac.wdl
    --soft-glob-output you must use this option because CRC NFS file system does not support hard link
    The software tools for this pipeline are installed as a singularity image at /ihome/crc/install/encode-dcc/atac-seq-pipeline-v2.2.0.sif
    This caper leader jobs will submit many Cromwell jobs to the slurm queue.

Check the slurm output file. The workflow runs successfully when you see the following line at the end of slurm output file.

“This workflow ran successfully. There is nothing to troubleshoot”

### Use croo to organize outputs

Find a metadata.json on Caper's output directory chip.

cd atac/42062d3e-f7a4-4759-ade6-c215ebcfd53a
module load caper/2.2.2
croo metadata.json