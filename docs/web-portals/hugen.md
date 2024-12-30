# **hugen or teach Open Ondemand webportal on the teach cluster**

## **Summary**

The hugen.crc.pitt.edu or teach.crc.pitt.edu Open Ondemand Web Portal is the gateway to CRC teach cluster to teach bioinformatics courses. Department of Human Genetics has paid part of the cost of this node. 

## **Request an Allocation for a Course**

<p>The course instructor can <a href="https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=KVCW5G08Bfo_&amp;RequestorType=Service">fill out this form</a> and attach a spreadsheet file containing the following information on each of the students from your class roster that will require access to the allocation's resources:</p>

<p>Name, Pitt Email (<a href="mailto:username@pitt.edu">username@pitt.edu</a>, no aliased emails)</p>

<p>The format of your course's user group name and slurm allocation will be SUBJECTNUMBER-YEAR(f/s) where the f/s denotes fall or spring semester. We will allocate 100K Sus on the teach cluster and 5TB group shared storage to your course.<strong> Duration of computing time and storage: active for 1 term ((4 months).</strong> We will provide you the slurm allocation name and the storage location when we reply to your ticket. In the below demonstration, we will use hugen2071-2024f as the slurm allocation name and the storage location is /ix1/hugen2071-2024f. <strong>Note that there is an expiration date for the 5TB storage allocation. We will delete the 5TB storage allocation without discussing with the instructors or students.</strong></p>

Based on the spreadsheet, we will create the linux group, i.e., hugen2071-2024f. We will add the students, the instructor and the TAs to the linux group. 

Note that a user can be associated with multiple linux groups. Running `id <username>` will show `gid=<primary group>`. hugen2071-2024f may not be your primary linux group.

/ix1/hugen2071-2024f is owned by the instructor and the linux group hugen2071-2024f. The default permission is 770 so that members of the group hugen2071-2024f have written permissions to this folder. 

We will create the slurm account hugen2071-2024f and add the students, the instructor and the TAs to this slurm account. We will allocate 100K Sus on the teach cluster to the slurm account. Note that a user can be associated with multiple slurm accounts. You can use `sacctmgr list user <username>` to check your default slurm account.

## **[Log in to hugen or teach](https://hugen.crc.pitt.edu, https://teach.crc.pitt.edu)**

The portal server is firewalled within PittNet and as such [you will need to be on VPN](https://crc.pitt.edu/user-support/resource-documentation/vpn-and-accessing-clusters) or be directly connected to PittNet via Ethernet.

<p>If you are off-campus, the clusters are accessible securely from almost anywhere in the world via the PittNet Virtual Private Network&nbsp;(VPN), which is administered by Pitt IT. The VPN requires certain software to run on your system. We recommend Global Protect.</p>

Once connected, you can run commands directly on the server and submit batch jobs to the CRC clusters. You can click >_ teach Shell Access under Clusters to get a Linux Shell Access.

![](../_assets/img/web-portals/hugen1.png)

All CRC software can be accessed using a terminal and our LMOD software provisioning system.

<p><strong><a href="https://crc.pitt.edu/user-support/installed-software/cluster-application-environment">https://crc-pages.pitt.edu/user-manual/applications/application-environment/</a></strong></p>

<p><a href="https://www.tacc.utexas.edu/research-development/tacc-projects/lmod" target="_blank">Lmod</a>&nbsp;will be used by cluster administrators to provide optimized builds of commonly used software. Applications are available to users through the&nbsp;<a href="https://www.tacc.utexas.edu/research-development/tacc-projects/lmod" target="_blank">Lmod</a>&nbsp;modular environment commands. There are no default modules loaded when you log in. You can use this system to teach the course. You can also create conda environment and install your own tools.</p>

<p>The instructor can create a folder under the course storage to install conda environments.</p>

<pre>
[fangping@login3 ~]$ cd /ix1/hugen2071-2024f
[fangping@login3 hugen2071-2024f]$ mkdir software</pre>

<p>We are using slurm as workload manager. To use the Sus from the course, you can use --account=hugen2071-2024f to charge resources used by this job to the specified course account. We recommend the instructor to provide slurm job templates.</p>

<h1>Using R</h1>

<p><strong><a href="https://crc.pitt.edu/r_and_rstudio">https://crc-pages.pitt.edu/user-manual/applications/r%2Brstudio/</a></strong></p>

<p>We have installed multiple R modules. &nbsp;You can use module spider r to view available modules and module spider r/version to show how to load the module. The latest r/4.3.0 can be loaded as:</p>

<pre>
[fangping@login0b demo]$ srun --account=course-2023s --pty bash
[fangping@htc-1024-n3 demo]$ module load gcc/12.2.0 r/4.3.0
[fangping@htc-1024-n3 demo]$ R

R version 4.3.0 (2023-04-21) -- "Already Tomorrow"
Copyright (C) 2023 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

&gt;</pre>

<p>Within each R module, various R packages and bioconductor packages have been installed. For the above r/4.3.0, the location of these packages is /ihome/crc/install/gcc-12.2.0/r/4.3.0/lib64/R/library. Within the R console, load the library to check whether it is already installed.</p>

<p>You can also install your own R packages. R searches the user’s path for libraries followed by the root installation. R will stop searching when it finds the first instance of the library within the path hierarchy.Use “.libPaths()” to check the searching path. For r/4.3.0, your local R packages will be installed under ~/R/x86_64-pc-linux-gnu-library/4.3. To allow all attendees to use the same R packages, we recommend that the instructor hides his/her local R packages. If you need specific R packages for your course, submit a help ticket, and we will install the package so that all attendees can use the same version.</p>

<p>You can also use RStudio server on Open Ondemand to teach the course. Logon ondemand.htc.crc.pitt.edu, Select Interactive Apps -&gt; RStudio Server 2022</p>

<p><img alt="" height="751" src="/sites/default/files/course_R_1.png" width="600" /></p>

<p>Click Launch to start RStudio server. Slurm will submit a batch job to request 2 cores (16 GB memory), 2 hour walltime. The RStudio server will be run by the login user and the rsession will run the specified R version. The SUs will be extracted from the slurm account course-2023s.</p>

<p><img alt="" height="475" src="/sites/default/files/course_R_2.png" width="1000" /></p>

<p>By default, the working directory of the R session is the home directory. You can use setwd() to change the working directory. For example, you can guide each student to change the working directory as shown above.</p>

<p>The instructor should mkdir the parent folder /ix/genomics/demo/users and chmod it to be group writable.</p>

<pre>
[fmu@login0b ~]$ mkdir -p /ix/genomics/demo/users
[fmu@login0b ~]$ chmod 770 /ix/genomics/demo/users
</pre>

<p>The instructor can guide each student to create his/her own folder under the course storage. Each student can logon ondemand.htc.crc.pitt.edu, click Clusters -&gt; &gt;_HTC Shell Access</p>

<pre>
[fmu@login0b ~]$ mkdir -p /ix/genomics/demo/users/fmu
[fmu@login0b ~]$ chmod 700 /ix/genomics/demo/users/fmu
</pre>

<p>You can open a R markdown file. I have copied pbmc3k_tutorial.Rmd from NGS 2022s workshops.</p>

<pre>
[fangping@login0b ~]$ cd /ix/genomics/demo/users/fmu
[fangping@login0b fmu]$ cp /bgfs/genomics/workshops/2022s/Overview_of_NGS_data_analysis_using_Pitt_ondemand_and_R/seurat/pbmc3k_tutorial.Rmd .</pre>

<p>Then click Open File from RStudio server and open pbmc3k_tutorial.Rmd. You can Knit to html to generate html output from the R markdown. &nbsp;</p>

<p><img alt="" height="599" src="/sites/default/files/ondemand_R_5.png" width="1000" /></p>

<p>The instructor can also teach students how to submit a R batch job. We recommend that the instructors provide a job template (test.sbatch) as follows.</p>

<pre>
#!/bin/bash
#SBATCH --job-name R_ExampleJob
#SBATCH --account=course-2023s # use your course allocation
#SBATCH --nodes=1  # request a single node
#SBATCH -c 1  # request 1 core
#SBATCH --time=01:00:00  # 1 hour walltime

# load R module
module load gcc/12.2.0 r/4.3.0

#the instructors or students can write the R code in test.R

R CMD BATCH test.R test.txt
# R CMD BATCH test.R #output will be directed to test.Rout</pre>

<p>To submit this job, run "sbatch test.sbatch"</p>

<p>The instructor can also teach other advance topics, including parallel processing or high throughput computing jobs. You can refer to https://crc.pitt.edu/r_and_rstudio</p>

<h1><strong>Using conda and python</strong></h1>

<p>We have installed multiple anaconda python as modules and these modules can also be used through Open Ondemand Jupyter notebook/lab.</p>

<p>python/ondemand-jupyter-python3.8</p>

<p>python/ondemand-jupyter-python3.9</p>

<p>python/ondemand-jupyter-python3.10</p>

<p>Each anaconda python distribution includes more than 6000 python packages.</p>

<p>You can select the corresponding module, and run jupyter notebook/lab using the anaconda python distribution. Logon ondemand.htc.pitt.edu, Interactive Apps -&gt; Jupyter</p>

<p><img alt="" height="669" src="/sites/default/files/ondemand_python.png" width="500" /></p>

<p>&nbsp;</p>

<p>Conda is an open source package management system and environment management system. The instructor can generate conda environment and use conda as a package manager to install, run and update packages and their dependencies. We recommend that the instructor generate conda environment(s) under the course storage. All course attendees should use this conda environment(s).</p>

<pre>
[fangping@login0b ~]$ cd /ix/genomics/demo/software
[fangping@login0b software]$ module load python/ondemand-jupyter-python3.10
[fangping@login0b software]$ conda create --prefix=/ix/genomics/demo/software/env python=3.10
...
[fangping@login0b software]$ source activate /ix/genomics/demo/software/env
(/ix/genomics/demo/software/env) [fangping@login0b software]$</pre>

<p>DO NOT activate your environment using “conda activate”</p>

<p>Now the instructor can install software packages related to the course to the conda environment.</p>

<p><strong>Bioconda</strong> lets you install thousands of software packages related to biomedical research using the conda package manager.</p>

<p><a href="https://bioconda.github.io/">https://bioconda.github.io/</a></p>

<p><a href="https://bioconda.github.io/recipes/hisat2/README.html">https://bioconda.github.io/recipes/hisat2/README.html</a></p>

<pre>
(/ix/genomics/demo/software/env) [fangping@login0b software]$ conda install hisat2</pre>

<p>...</p>

<p>To start a Jupyter Lab/notebook with this conda environment activated, you can specify the location of the conda environment under “Name of Custom Conda Environment”.</p>

<p><img alt="" height="710" src="/sites/default/files/ondemand_python_2.png" width="500" /></p>

<p>If you receive this error "Failed to connect to htc-n??.crc.pitt.edu:?????" when you Lauch the Jupyter. Wait 1-2 minuates, then refresh your browser.</p>

<p>To use the python packages inside this conda environment, you can directly import the python package. To demonstrate the other packages that you have installed in the conda environment, you can run it through jupyter notebook (See the hisat2 example below).</p>

<p><img alt="" height="701" src="/sites/default/files/ondemand_python_3.png" width="1000" /></p>

<p>You can also run the other packages through terminal. Launcher -&gt; Terminal</p>

<p><img alt="" height="683" src="/sites/default/files/ondemand_python_4.png" width="700" /></p>

<p>When you start Jupyter Notebook/Lab, the working directory is the home directory. You can use softlink trick to navigate to course storage.</p>

<p>Logon ondemand.htc.crc.pitt.edu, click Clusters -&gt; &gt;_HTC Shell Access</p>

<pre>
[fmu@login0b ~]$ mkdir -p /ix/genomics/demo/users/fmu # create a folder
[fmu@login0b ~]$ chmod 700 /ix/genomics/demo/users/fmu # change the permission
[fmu@login0b ~]$ ln -s /ix/genomics/demo/users/fmu my_course_data # generate a softlink to home directory
[fmu@login0b ~]$</pre>

<p>Each student can navigate to his/her own my_course_data from Jupyter Lab.</p>

<p>The instructor can also teach students how to submit a batch job using the conda environment or other CRC modules. We recommend that the instructors provide a job template (test.sbatch) as follows.</p>

<pre>
#!/bin/bash
#SBATCH --job-name ExampleJob
#SBATCH --account=course-2023s # use your course allocation
#SBATCH --nodes=1  # request a single node
#SBATCH -c 1  # request 1 core
#SBATCH --time=01:00:00  # 1 hour walltime

# use the custom conda environment
module load python/ondemand-jupyter-python3.10
source activate /ix/genomics/demo/software/env

# You can also use other modules
# module load hisat2/2.2.1

hisat2 --help # run your commands</pre>

<p>To submit this job, run "sbatch test.sbatch"</p>
