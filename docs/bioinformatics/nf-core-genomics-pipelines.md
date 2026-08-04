# nf-core Pipelines

Nextflow is a reactive workflow framework and programming DSL designed to simplify the creation of data-intensive computational pipelines.

Key characteristics:

*  A workflow-oriented programming language built on Groovy/Java
*  Particularly well-suited for complex, highly parallel bioinformatics pipelines
*  Designed for ease of use
*  Manages interaction with compute infrastructure, enabling execution across nearly any environment

[nf-core](https://nf-co.re/) is a community-driven initiative that maintains a curated collection of analysis [pipelines](https://nf-co.re/pipelines) built with Nextflow.

I built a web application on Open OnDemand that lets you:

*  Select a pipeline
*  Complete the launch form for that pipeline
*  Submit the pipeline to the HTC cluster with your selected options

To get started:

Navigate to https://ondemand.htc.crc.pitt.edu

1. Click Genomics Apps → nf-core pipelines

![](../_assets/img/advanced-genomics-support/nf-core-1.png)

2. Select a pipelne, for example, rnaseq 3.22.2, then click Launch

![](../_assets/img/advanced-genomics-support/nf-core-2.png)

3. Click Connect to Nextflow

![](../_assets/img/advanced-genomics-support/nf-core-3.png)

4. Complete the webform, then click "Launch Workflow".

## Guidelines

1. /vast/bioinformatics/tutorials/nf-core-rnaseq serves as an example setup for nf-core rnaseq 3.22.2.
2. Before running the pipeline, you'll need to create a samplesheet containing information about the samples you want to analyze. Provide the absolute path to this input file. As shown in the screenshot above, the samplesheet path used is /vast/bioinformatics/tutorials/nf-core-rnaseq/samples.csv.
3. The pipeline runs from the samplesheet's parent directory, and both -work-dir and outdir are relative to this path. In the example above, the parent path is /vast/bioinformatics/tutorials/nf-core-rnaseq, making the working directory /vast/bioinformatics/tutorials/nf-core-rnaseq/work and the output directory /vast/bioinformatics/tutorials/nf-core-rnaseq/results.
4. After clicking Launch, an nf-params.json file and a job.sbatch file will be generated in the parent directory. The job.sbatch file is then automatically submitted to run the nf-core pipeline.
5. The default config file is /software/rhel9/manual/install/nf-core/pipelines/config/htc.config. Leaving -profile empty will use this default. Alternatively, you can create a custom config file and specify its absolute path in -profile. /vast/bioinformatics/tutorials/nf-core-rnaseq/htc.config is another config file, where clusterOptions has been modified to specify a particular Slurm account.
6. All nf-core pipelines are located in /software/rhel9/manual/install/nf-core/pipelines.
7. Nextflow is built for running highly parallel bioinformatics pipelines, which means it relies on a fast file system to perform well. For this reason, we recommend running Nextflow pipelines on CRCD's /vast file system. If you're instead using CRCD's warm storage (/ix or /ix1), limit your pipeline runs to fewer than 10 samples.

## Installed pipelines

[sopa 1.0.0](https://nf-co.re/sopa/1.0.0)  
[spatialaxe 1.0.0](https://nf-co.re/nf-core-spatialaxe/1.0.0)  
[airrflow 4.3.1](https://nf-co.re/airrflow/4.3.1)  
[ampliseq 2.15.0](https://nf-co.re/ampliseq/2.15.0)  
[atacseq 2.1.2](https://nf-co.re/atacseq/2.1.2)  
[bacass 2.5.0](https://nf-co.re/bacass/2.5.0)  
[bactmap 1.0.0](https://nf-co.re/bactmap/1.0.0)  
[bamtofastq 2.2.0](https://nf-co.re/bamtofastq/2.2.0)  
[chipseq 2.1.0](https://nf-co.re/chipseq/2.1.0)  
[circdna 1.1.0](https://nf-co.re/circdna/1.1.0)  
[cutandrun 3.2.2](https://nf-co.re/cutandrun/3.2.2)  
[demultiplex 1.7.0](https://nf-co.re/demultiplex/1.7.0)  
[denovotranscript 1.2.1](https://nf-co.re/denovotranscript/1.2.1)  
[differentialabundance 1.5.0](https://nf-co.re/differentialabundance/1.5.0)  
[drugresponseeval 1.1.0](https://nf-co.re/drugresponseeval/1.1.0)  
[fetchngs 1.12.0](https://nf-co.re/fetchngs/1.12.0)  
[funcscan 3.0.0](https://nf-co.re/funcscan/3.0.0)  
[genomeassembler 1.1.0](https://nf-co.re/genomeassembler/1.1.0)  
[hgtseq 1.1.0](https://nf-co.re/hgtseq/1.1.0)  
[hic 2.1.0](https://nf-co.re/hic/2.1.0)  
[hlatyping 2.1.0](https://nf-co.re/hlatyping/2.1.0)  
[isoseq 2.0.0](https://nf-co.re/isoseq/2.0.0)  
[mag 5.3.0](https://nf-co.re/mag/5.3.0)  
[metatdenovo 1.3.0](https://nf-co.re/metatdenovo/1.3.0)  
[methylseq 4.2.0](https://nf-co.re/methylseq/4.2.0)  
[mhcquant 3.0.0](https://nf-co.re/mhcquant/3.0.0)  
[nanoseq 3.1.0](https://nf-co.re/nanoseq/3.1.0)  
[oncoanalyser 2.3.0](https://nf-co.re/oncoanalyser/2.3.0)  
[pacvar 1.0.1](https://nf-co.re/pacvar/1.0.1)  
[pangenome 1.1.3](https://nf-co.re/pangenome/1.1.3)  
[pathogensurveillance 1.0.0](https://nf-co.re/pathogensurveillance/1.0.0)  
[raredisease 2.6.0](https://nf-co.re/raredisease/2.6.0)  
[rnafusion 4.0.0](https://nf-co.re/rnafusion/4.0.0)  
[rnaseq 3.22.2](https://nf-co.re/rnaseq/3.22.2)  
[rnasplice 1.0.4](https://nf-co.re/rnasplice/1.0.4)  
[rnavar 1.2.2](https://nf-co.re/rnavar/1.2.2)  
[sarek 3.7.1](https://nf-co.re/sarek/3.7.1)  
[scnanoseq 1.2.1](https://nf-co.re/scnanoseq/1.2.1)  
[scrnaseq 4.1.0](https://nf-co.re/scrnaseq/4.1.0)  
[taxprofiler 1.2.5](https://nf-co.re/taxprofiler/1.2.5)  
[viralmetagenome 1.0.1](https://nf-co.re/viralmetagenome/1.0.1)  
[viralrecon 3.0.0](https://nf-co.re/viralrecon/3.0.0)  


[mcmicro 2.0.0](https://nf-co.re/jul/2026/nf-core-mcmicro/2.0.0)  


[proteinfamilies 2.4.0](https://nf-co.re/jul/2026/nf-core-proteinfamilies/2.4.0)  
[proteinannotator 1.1.0](https://nf-co.re/jul/2026/nf-core-proteinannotator/1.1.0)  
[proteinfold 2.0.0](https://nf-co.re/jul/2026/nf-core-proteinfold/2.0.0)  


[quantms 1.8.0](https://nf-co.re/jul/2026/nf-core-quantms/1.8.0)  

** nf-core rnaseq 3.22.2
---------------------------------------------

This walkthrough demonstrates how to run nf-core/rnaseq 3.22.2 on an actual RNA-seq dataset.

1. Go to https://ondemand.htc.crc.pitt.edu and log in with your Pitt credentials.
2. Click Files to confirm you have access to a /vast folder. Alternatively, click Home Directory, then use Change directory to navigate to your group's folder. In this example, we've moved to /vast/bioinformatics/tutorials.
3. Use New Directory to create a new folder. Here, we've named it nf-core-rnaseq.
4. Inside nf-core-rnaseq, create a subfolder called fastqs and move your raw sequencing files there. This example uses 12 fastq.gz files uploaded to that folder. To upload your own raw data, follow one of the methods described in this manual: https://crc-pages.pitt.edu/user-manual/data-management/. Do not use OnDemand's built-in Upload feature for this, since it's meant for small files only, not large sequencing datasets.
5. Return to the nf-core-rnaseq folder, click New File, and name it samples.csv.
6. Click Edit to open samples.csv.
7. Paste in your nf-core samplesheet content and click Save.
8. Get the absolute path to samples.csv. Clicking copy path gives you the parent directory path — in this case, /vast/bioinformatics/tutorials/nf-core-rnaseq — so the full path to the file is /vast/bioinformatics/tutorials/nf-core-rnaseq/samples.csv.
9. Click Genomics Apps → nf-core pipelines, choose rnaseq 3.22.2, and click Launch.
10. Click Connect to Nextflow.
11. Enter the absolute path to samples.csv in the input field, and set the outdir field to results.
12. Under Reference genome options, select GRCh38 as the genome.
13. Under Alignment options, default settings were used for this example.
14. Under Optional outputs, the save_references box was checked, so the STAR index gets saved in the results directory for future reuse.
15. Scroll to the bottom and click Launch workflow.
16. Once submitted, you'll receive two emails: one when the pipeline starts and one when it finishes.
17. On successful completion, results will be in the results folder. If the run fails, enable Show Dotfiles and check .nextflow.log to troubleshoot.
18. A good starting point for reviewing results is downloading multiqc_report.html and looking through it.


RNA sequencing analysis pipeline using STAR, RSEM, HISAT2 or Salmon with gene/isoform counts and extensive quality control.

1. Logon ondemand.htc.crc.pitt.edu, Click Files -> Home Directory, Click Change directory and go to your bgfs or zfs folder. You can go to ```/bgfs/genomics/fangping/nf-core-rnaseq``` to see an example.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-1.png)

Create a folder fastqs, and transfer your fastqs to this folder. 6 paired-end fastqs from GSE155987 have been downloaded to ```/bgfs/genomics/fangping/nf-core-rnaseq/fastqs```.

Differential Gene Expression Analysis Reveals Global LMTK2 Regulatory Network and Its Role in TGF-β1 Signaling. Cruz DF, Mitash N, Mu F, Farinha CM, Swiatecka-Urban A. Front Oncol. 2021 Mar 18;11:596861. doi: 10.3389/fonc.2021.596861.

The raw fastqs files are within this folder.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-2.png)

2. Samplesheet input

You will need to create a samplesheet file with information about the samples in your experiment before running the pipeline. Use this parameter to specify its location. It has to be a comma-separated file with 5 columns, and a header row specified in [this documentation](https://nf-co.re/rnaseq/2.0/usage).

You can use editor on Open Ondemand to edit this file.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-3.png)

Keep a record of this samplesheet location. You can get its location from the header line of the above editor. For this example, this file is at ```/bgfs/genomics/fangping/nf-core-rnaseq/samples.csv```

3. Reference genome

Create a folder refs and download the reference genome for your samples. I have downloaded Ensembl homo sapiens GRCh38 primary assembly and Ensembl annotation version 103 to ```/bgfs/genomics/fangping/nf-core-rnaseq/refs```

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-4.png)

4. Fill the launch form

Click Genomics Apps -> nf-core pipelines, choose rnaseq 2.0, click Launch, then click Connect to Nextflow

The input field is the absolute path to your sample sheet.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-5.png)

The minimum reference genome requirements are a FASTA and GTF file, all other files required to run the pipeline can be generated from these files. However, it is more storage and compute friendly if you are able to re-use reference genome files as efficiently as possible. It is recommended to change the save\_reference parameter to be true if you are using the pipeline to build new indices.

fasta and gtf fields are the the absolute path to your fasta and gtf file.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-6.png)

If you are using the pipeline to build new indices, change save_reference to be True. The star index is available at ```/bgfs/genomics/fangping/nf-core-rnaseq/results/genome/index/star```, and you can use this folder as input to star_index for future runs.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-7.png)

Choose the aligner and pseudo_aligner.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-8.png)

Adjust other parameters based on your data analysis goals. The meanings of these parameters are available [here](https://nf-co.re/rnaseq/2.0/parameters).

5. Lauch workflow

When you are ready, click Launch workflow.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-9.png)

You can close this launch form. You will be notified by emails when your workflow begins and ends.

6. Outputs

[This document](https://nf-co.re/rnaseq/2.0/output) describes the output produced by the pipeline. For the above example, featurecounts.merged.counts.tsv is under /bgfs/genomics/fangping/nf-core-rnaseq/results/star/

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-10.png)

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-11.png)

Navigate to ```/bgfs/genomics/fangping/nf-core-rnaseq/results/salmon```, you can find:

```salmon.merged.gene_counts.tsv```: Matrix of gene-level raw counts across all samples.

```salmon.merged.gene_tpm.tsv```: Matrix of gene-level TPM values across all samples.

```salmon.merged.transcript_counts.tsv```: Matrix of isoform-level raw counts across all samples.

```salmon.merged.transcript_tpm.tsv```: Matrix of isoform-level TPM values across all samples.

Navigate to ```/bgfs/genomics/fangping/nf-core-rnaseq/results/multiqc/star```, and click ```multiqc\_report.html``` to open the multiqc results.

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-13.png)

![](../_assets/img/advanced-genomics-support/nf-core-rnaseq-14.png)

If you prefer to submit jobs through command line iapproach, use ```/bgfs/genomics/fangping/nf-core-rnaseq/job.sbatch```, input.json as reference. Both files are automatically generated through the above web application.

[nf-core/sarek](https://nf-co.re/sarek/2.7)
-------------------------------------------

Analysis pipeline to detect ```germline``` or somatic variants (pre-processing, variant calling and annotation) from WGS / targeted sequencing. Initially designed for Human, and Mouse, it can work on any species with a reference genome. Sarek can also handle tumour / normal pairs and could include additional relapses.

TODO

[nf-core/chipseq](https://nf-co.re/chipseq/1.2.2)
-------------------------------------------------

ChIP-seq peak-calling, QC and differential analysis pipeline. ```nfcore/chipseq``` is a bioinformatics analysis pipeline used for Chromatin ImmunopreciPitation sequencing (ChIP-seq) data.

1. Logon ondemand.htc.crc.pitt.edu, Click Files -> Home Directory, Click Change directory and go to your bgfs or zfs folder. Test data are in the folder /bgfs/genomics/fangping/nf-core-chipseq/tf and /bgfs/genomics/fangping/nf-core-chipseq/histone

![](../_assets/img/advanced-genomics-support/chipseq-0.png)

2. H. Sapiens single-end ChIP-seq dataset was obtained from 2 separate studies.

**Transcription factor data**

[Pubmed](https://pubmed.ncbi.nlm.nih.gov/25752574/), [GEO](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE59530) (Input samples: SRR1635435 - SRR1635438, samples: SRR1635459 - SRR1635462)

I have downloaded these data to ```/bgfs/genomics/fangping/nf-core-chipseq/tf/fastqs```

![](../_assets/img/advanced-genomics-support/chipseq-00.png)

**Broad histone data**

[Pubmed](https://pubmed.ncbi.nlm.nih.gov/25188243/), [GEO](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE57632) (samples: SRR1285070 - SRR1285073, Input samples: SRR1285074 - SRR1285077)

I have downloaded these data to ```/bgfs/genomics/fangping/nf-core-chipseq/histone/fastqs```

3. Samplesheet input file

You will need to create a design file with information about the samples in your experiment before running the pipeline. Use this parameter to specify its location. It has to be a comma-separated file with 6 columns, and a header row contains the following columns.

group,replicate,fastq\_1,fastq\_2,antibody,control

![](../_assets/img/advanced-genomics-support/chipseq-sample-0.png)

![](../_assets/img/advanced-genomics-support/chipseq-sample-1.png)

4. Fill the launch form

Click Genomics Apps -> nf-core pipelines, choose chipseq 1.2.1, click Launch, then click Connect to Nextflow

### Transcription factor data

The input field is the absolute path to your sample sheet. Choose single\_end to be True.

![](../_assets/img/advanced-genomics-support/chipseq-1.png)

These are human data. We used hg38 as reference genome. You can also provide the absolute path to fasta and gtf files.

![](../_assets/img/advanced-genomics-support/chipseq-2.png)

Run MACS2 in narrowPeak mode because these are transcription factor data.

![](../_assets/img/advanced-genomics-support/chipseq-3.png)

Click Launch workflow.

![](../_assets/img/advanced-genomics-support/chipseq-4.png)

Histone Data

The input field is the absolute path to your sample sheet. Choose ```single_end``` to be True.

![](../_assets/img/advanced-genomics-support/chipseq-5.png)

These are human data. We used hg38 as reference genome.

![](../_assets/img/advanced-genomics-support/chipseq-6.png)

Run MACS2 in broadPeak mode because these are histone data.

![](../_assets/img/advanced-genomics-support/chipseq-7.png)

Click Launch workflow.

![](../_assets/img/advanced-genomics-support/chipseq-8.png)

6. Outputs

[This document](https://nf-co.re/chipseq/1.2.1/output) [](https://nf-co.re/mag/2.0.0/output)describes the output produced by the pipeline. Navigate to ```/bgfs/genomics/fangping/nf-core-chipseq/tf/results and /bgfs/genomics/fangping/nf-core-chipseq/histone/results``` to read the results.

![](../_assets/img/advanced-genomics-support/chipseq-results-1.png)

Navigate to ```/bgfs/genomics/fangping/nf-core-chipseq/tf/results/multiqc/narrowPeak``` and ```/bgfs/genomics/fangping/nf-core-chipseq/histone/results/multiqc/broadPeak``` to go through the multiqc report.

![](../_assets/img/advanced-genomics-support/chipseq-results-2.png)

Click multiqc\_report.html to open the report.

![](../_assets/img/advanced-genomics-support/chipseq-results-3.png)

[nf-core/atacseq](https://nf-co.re/atacseq/1.2.1)
-------------------------------------------------

ATAC-seq peak-calling, QC and differential analysis pipeline. ```nfcore/atacseq``` is a bioinformatics analysis pipeline used for ATAC-seq data.

1. Logon ondemand.htc.crc.pitt.edu, Click Files -> Home Directory, Click Change directory and go to your bgfs or zfs folder. ATACseq test data are in the folder ```/bgfs/genomics/fangping/nf-core-atacseq```

![](../_assets/img/advanced-genomics-support/atacseq-1.png)

2. Within the fastqs folder, I have downloaded 6 samples from [this paper](https://pubmed.ncbi.nlm.nih.gov/29636475/).

![](../_assets/img/advanced-genomics-support/atacseq-2.png)

3. Samplesheet input file

You will need to create a design file with information about the samples in your experiment before running the pipeline. Use this parameter to specify its location. It has to be a comma-separated file with 4 columns.

  
group,replicate,fastq\_1,fastq\_2

Example ```samplesheet``` is at ```/bgfs/genomics/fangping/nf-core-atacseq/input.tsv```

![](../_assets/img/advanced-genomics-support/atacseq-3.png)

4. Fill the launch form

Click Genomics Apps -> nf-core pipelines, choose atacseq 1.2.1, click Launch, then click Connect to Nextflow

The input field is the absolute path to your sample sheet.

![](../_assets/img/advanced-genomics-support/atacseq-10.png)

These are human data. We used hg38 as reference genome. You can also provide the absolute path to fasta and gtf files.

![](../_assets/img/advanced-genomics-support/atacseq-11.png)

Run MACS2 in narrowPeak mode.

![](../_assets/img/advanced-genomics-support/atacseq-12.png)

5. Click Launch workflow.

![](../_assets/img/advanced-genomics-support/atacseq-13.png)

6. Outputs

[This document](https://nf-co.re/atacseq/1.2.1/output) describes the output produced by the pipeline. Navigate to ```/bgfs/genomics/fangping/nf-core-atacseq/results``` to read the results.

![](../_assets/img/advanced-genomics-support/atacseq-20.png)

Navigate to ```/bgfs/genomics/fangping/nf-core-atacseq/results/multiqc/narrowPeak``` to go through the multiqc report.

![](../_assets/img/advanced-genomics-support/atacseq-21.png)

Click ```multiqc_report.html``` to open the report.

![](../_assets/img/advanced-genomics-support/atacseq-22.png)

[nf-core/mag](https://nf-co.re/mag/2.0.0)
-----------------------------------------

Assembly and binning of metagenomes. nf-core/mag is a bioinformatics best-practise analysis pipeline for assembly, binning, and annotation of metagenomes.

By default, the pipeline currently performs the following: it supports both short and long reads, quality trims the reads and adapters with fastp and Porechop, and performs basic QC with FastQC. The pipeline then:

*   assigns taxonomy to reads using Centrifuge and/or Kraken2
*   performs assembly using MEGAHIT and SPAdes, and checks their quality using Quast
*   performs metagenome binning using MetaBAT2, and checks the quality of the genome bins using Busco
*   assigns taxonomy to bins using GTDB-Tk and/or CAT

Furthermore, the pipeline creates various reports in the results directory specified, including a MultiQC report summarizing some of the findings and software versions.

![](../_assets/img/advanced-genomics-support/mag.png)

1. Logon ondemand.htc.crc.pitt.edu, Click Files -> Home Directory, Click Change directory and go to your bgfs or zfs folder. Test data for mag is in the folder /bgfs/genomics/fangping/nf-core-mag

![](../_assets/img/advanced-genomics-support/mag-1.png)

2. Within the fastqs folder, I have downloaded the gut metagenome data of antibiotic-treated patients originating from [Bertrand et al. Nature Biotechnology (2019)](https://www.nature.com/articles/s41587-019-0191-2). Three samples have been used. Note that sequencing data for the 3 samples have both Illumina and ONT reads.

SAMPLE

ILLUMINA READS: ENA ID

ONT READs: ENA ID

CAPES_S7

ERR3201914

ERR3201938

CAPES\_S11

ERR3201918

ERR3201942

CAPES_S21

ERR3201928

ERR3201952

![](../_assets/img/advanced-genomics-support/mag-2.png)

3. Samplesheet input file

To assign different groups or to include long reads for hybrid assembly with metaSPAdes, you can specify a CSV samplesheet input file that contains the paths to your FASTQ files and additional metadata.

This CSV file should contain the following columns:

sample,group,short\_reads\_1,short\_reads\_2,long\_reads

The path to long\_reads and short\_reads\_2 is optional. Example samplesheet is at /bgfs/genomics/fangping/nf-core-mag/samples.csv

![](../_assets/img/advanced-genomics-support/mag-3.png)

4. Fill the launch form

Click Genomics Apps -> nf-core pipelines, choose mag 2.0.0, click Launch, then click Connect to Nextflow

The input field is the absolute path to your sample sheet.

![](../_assets/img/advanced-genomics-support/mag-4.png)

I have kept all other options as default values. Note that Taxonomic classification is disabled by default. cat\_db, centrifuge\_db and kraken2\_db are available under /bgfs/genomics/refs/metagenome\_refs

![](../_assets/img/advanced-genomics-support/mag-5.png)

5. Lauch workflow

When you are ready, click Launch workflow.

6. Outputs

[This document](https://nf-co.re/rnaseq/2.0/output) [](https://nf-co.re/mag/2.0.0/output)describes the output produced by the pipeline. Navigate to /bgfs/genomics/fangping/nf-core-mag/results to read the results.

![](../_assets/img/advanced-genomics-support/mag-6.png)

Navigate to /bgfs/genomics/fangping/nf-core-mag/results/multiqc to view the multiqc results.

![](../_assets/img/advanced-genomics-support/mag-7.png)

Click multiqc\_report.html to read the multiqc report.

![](../_assets/img/advanced-genomics-support/mag-8.png)
