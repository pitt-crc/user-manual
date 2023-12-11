Introduction to Singularity
---------------------------

Containerization of software runtime environment and processing workloads has become popular, particularly using Docker. However, Docker is not suitable for HPC applications [due to security reasons](#FAQ). There are a couple of alternatives for HPC containers, with Singularity being the one that covers a large set of cases. Singularity can import Docker containers.

Singularity is a _container platform_ that was created to run complex applications on HPC clusters in a simple, portable, and reproducible way. Singularity containers are a single file, and you don’t have to worry about how to install all the software you need on different operating systems and hardware.

Pulling Existing Images onto the Clusters
-----------------------------------------

Singularity has been installed as modules.
```
[fangping@login0b ~]$ module spider singularity

---------------------------------------------------------------------------------------------
  singularity:
---------------------------------------------------------------------------------------------
    Description:
      The Singularity container manager

     Versions:
        ...
        singularity/3.8.3
        singularity/3.9.6
```
### Image Repositories

[DockerHub](https://hub.docker.com) contains containers for various software packages, which can be directly used with Singularity.

[Quay.io](https://quay.io/) is a registry for storing and building container images as well as distributing other OCI artifacts.

[NVIDIA ngc](https://catalog.ngc.nvidia.com/) is a registry for GPU\-optimized containers.

Some software is provided as a Singularity image with the .sif or .img file extension, for example, the [Sylabs Container Library](https://cloud.sylabs.io/library). More commonly, however, a Docker image will be provided and this must be converted to a Singularity image. Go to hub.docker.com and search the tool name. For example, [https://hub.docker.com/r/biocontainers/bowtie2](https://hub.docker.com/r/biocontainers/bowtie2), the docker image is at biocontainers/bowtie2. You can also use the specific version, [https://hub.docker.com/r/biocontainers/bowtie2/tags](https://hub.docker.com/r/biocontainers/bowtie2/tags) the docker image for v2.4.1\_cv1 is biocontainers/bowtie2:v2.4.1\_cv1

The [BioContainers](https://biocontainers.pro/) project comprises container images automatically generated from [Bioconda](https://bioconda.github.io/) software packages. Search your tool at [https://bioconda.github.io/](https://bioconda.github.io/) For example, [https://bioconda.github.io/recipes/bowtie2/README.html](https://bioconda.github.io/recipes/bowtie2/README.html), you can find the docker container location quay.io/biocontainers/bowtie2. You can also use specific version. [https://quay.io/repository/biocontainers/bowtie2?tab=tags](https://quay.io/repository/biocontainers/bowtie2?tab=tags) For example, the docker image location for 2.4.5--py36hfca12d5\_2 is quay.io/biocontainers/bowtie2: 2.4.5--py36hfca12d5\_2

### Pulling the Image

You can download and convert the Docker image to a Singularity image with:
```
module load singularity/3.8.3
singularity pull --name bowtie2-2.4.5.sif docker://quay.io/biocontainers/bowtie2: 2.4.5--py36hfca12d5\_2
```
This will produce the file ```bowtie2-2.4.5.sif``` in the current working directory.

Note that singularity pull requires squashfs, and this library is only installed on the login nodes. If you are using "singularity pull" on the computation nodes, you can load the squashfs-tools/4.4 module.
```
module load singularity/3.8.3
module load squashfs-tools/4.4
```
Using Singularity Images on the Clusters
----------------------------------------

### Binding external directories

Binding a directory to your Singularity container allows you to access files in a host system directory from within your container. When you are using modules singularity/3.8.3 and singularity/3.9.6, singularity will bind /ihome, /scratch, /bgfs, /zfs1, /zfs2 and /ix. You can also bind other directories into your Singularity container yourself. The process is described in detail in the [Singularity documentation](https://www.sylabs.io/guides/latest/user-guide/bind_paths_and_mounts.html#specifying-bind-paths). Some java programs, such as gatk, picard, will use /tmp as temporary directory. This directory has limited space. You can bind another directory to the /tmp directory inside the container, i.e., -B /ix/<yourgroup>/<mytmp>:/tmp

### Interactive Singularity containers

Once you have an image, you can interact with it in several ways. The shell command allows you to spawn a new shell within your container and interact with it as though it were a small virtual machine.
```
[fangping@login0b ~]$ srun -c 2 --pty bash
[fangping@htc-n20 ~]$ module load singularity/3.9.6
[fangping@htc-n20 ~]$ singularity shell /ihome/crc/install/glnexus/1.4.1/glnexus-1.4.1.sif
Singularity> which glnexus_cli
/usr/local/bin/glnexus_cli
Singularity> glnexus_cli --help
[190082] [2022-05-17 09:44:51.007] [GLnexus] [info] glnexus_cli release v1.4.1-0-g68e25e5 Aug 13 2021
[190082] [2022-05-17 09:44:51.007] [GLnexus] [warning] jemalloc absent, which will impede performance with high thread counts. See https://github.com/dnanexus-rnd/GLnexus/wiki/Performance
Usage: glnexus_cli [options] /vcf/file/1 .. /vcf/file/N
...
Singularity> exit
exit
[fangping@htc-n20 ~]$
```
### Singularity containers in Batch Jobs

The ```exec``` command allows you to execute a custom command within a container by specifying the image file.

In this example, singularity will be used to run an instance of the [deepvariant container](https://github.com/google/deepvariant/blob/r1.3/docs/deepvariant-exome-case-study.md).

To pull the docker container:

```module load singularity/3.8.3
singularity pull --name deepvariant-1.3.0.sif docker://google/deepvariant:1.3.0
```
The singularity image is located in the install directory:
```
/ihome/crc/install/deepvariant/deepvariant-1.3.0.sif
```
You can use the following job template to download the data.
```
/ix/genomics/deepvariant/wes/download.sbatch
```
Write a batch script to run the singularity command similar to this:
```
#!/bin/bash
# This file is called wes\_job.sbatch
#SBATCH -N 1 # Ensure that all cores are on one machine
#SBATCH -t 3-00:00 # Runtime in D-HH:MM
#SBATCH --cpus-per-task=8 # Request that ncpus be allocated per process.

module load singularity/3.8.3

mkdir -p output
mkdir -p output/intermediate\_results\_dir

singularity exec  \\
  /ihome/crc/install/deepvariant/deepvariant-1.3.0.sif \\
  /opt/deepvariant/bin/run_deepvariant \\
  --model_type WES \\
  --ref reference/GRCh38_no_alt_analysis_set.fasta \\
  --reads input/HG003.novaseq.wes_idt.100x.dedup.bam \\
  --regions input/idt_capture_novogene.grch38.bed \\
  --output_vcf output/HG003.output.vcf.gz \\
  --output_gvcf output/HG003.output.g.vcf.gz \\
  --num_shards $SLURM_CPUS_PER_TASK \\
  --intermediate_results_dir output/intermediate_results_dir
```
Submit the job like so:
```
[fangping@login0b wes]$ sbatch wes_job.sbatch
```
### Environment Variables

Singularity by default exposes all environment variables from the host inside the container. One can define an environment variable within the container as follows:
```
export SINGULARITYENV_MYVAR=Overridden
```
With the above definition, MYVAR will have the value "Overridden".
```
export SINGULARITYENV_FS_LICENSE=/ihome/crc/install/freesurfer/license/license_free_surfer.txt
export SINGULARITYENV_SUBJECTS_DIR=${PWD}
```
For example, the above two environmental variables FS\_LICENSE and SUBJECTS\_DIR are used for Freesurfer singularity image.

Building local LMOD modules from singularity images
---------------------------------------------------

You can converts containers to LMOD modules
```
mkdir -p ~/modulefiles
cd ~/modulefiles
cp -r /ihome/crc/modules/Core/glnexus myglnexus
```
Change the module name and version number. Change the singularity image within run\_fuction. Change the programs to be exposed within programs.

Finally you can do:
```
module use ~/modulefiles
module load myglnexus/1.4.1 # Change this to your module name and version number

glnexus\_cli --help # test your programs
```
GPU examples
------------

Running your containers with GPU support is quite similar to the above options.

The main differences for batch and interactive jobs is that you need to specify the cluster as GPU, and that you are using [Generic Resource Scheduling](https://slurm.schedmd.com/gres.html) (GRES) with GPUs as the resource.
```
#SBATCH --clusters=gpu 
#SBATCH --gres=gpu:1
```
With the corresponding arguments for interactive jobs being:
```
srun -M gpu --gres=gpu:1
```
The GRES argument is how you specify the number of GPU cards to use.

With these set, you can use the --nv option to [grant your containers GPU support at runtime](https://sylabs.io/guides/3.5/user-guide/gpu.html#nvidia-gpus-cuda).

The option works on singularity functions that run or execute containers.
```
srun -M gpu --gres=gpu:1 -n1 -t02:00:00 --pty bash
singularity shell --nv /ihome/crc/install/openpose/openpose.sif
```
Note: The CUDA runtime (in the container) and driver (on the compute node) versions need to be compatiable. When logged into a GPU compute node, you can check the driver version with \`nvidia-smi\`.

FAQ
---

### Why can't I use Docker?

Docker images are not secure becuase they allow users to gain root access to the compute nodes. Singularity effectively runs as the user running the command, and does not result in elevated access. Also, docker interacts with the slurm job scheduler in a way that causes resource requests and usages to not match up, making it difficult to keep job queueing fair for all users. In that the clusters are multi-user systems, we want to make sure people can work without worry that others are accessing their data or unfairly using up resources.