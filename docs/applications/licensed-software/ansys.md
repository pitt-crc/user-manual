# ANSYS 

This page contains info aimed at first time users of ANSYS on CRC, but may also be useful to more experienced users.

ANSYS is a suite of engineering simulation software. Below is a list of specific modules that the CRC is licensed to 
use under the academic license; the launch command for each application is included in brackets.

## Available Modules

### Material and Structural Mechanics:

- Autodyn (autodyn231)
- Ansys Composite PrePost (ACP)

### Visualization:
- Ansys Ensight (ensight)

### CFD, Heat Transfer, Turbomachinery, Meshing:
    
- Fluent (fluent)
- CFX (cfx5)
- CFD-Post (cfdpost)
- Polyflow (polyflow)
- ICEM CFD (icemcfd)
- Turbogrid (cfxtg)

### Electronics Cooling:

- Icepak (icepak)

### HPC interface:

- ANSYS RSM (rsmluancher)

## Access modalities for ANSYS

### Using graphic user interface (VIZ)

Please read the [documentation](../../web-portals/viz.md) on how to access VIZ. Once you have logged into VIZ, open 
a terminal window by right-clicking on the desktop background and select the "Open Terminal here" option from the 
drop-down menu, or click on the black terminal window symbol at the bottom of your screen. Inside the terminal, type 
`module load ansys` and hit enter. This will make the ANSYS 2023R1 suite available. To launch your specific ANSYS 
application, type its name as listed within the brackets above, such as "fluent" or "cfx5" and hit enter. Please 
be aware that the **capitalization** is important!  To launch the workbench, type "launcher". 

![](https://crc.pitt.edu/sites/default/files/Screenshot_ANSYS_VIZ.png)
<!-- TODO: replace with image hosted in assets -->

### Using command line interface: CFX as example

Login to the cluster via terminal and copy the directory `/ihome/crc/how_to_run/ansys/CFX/SMP` which contains the 
template Slurm job script and CFX input file into your own directory, such as `CFX_example` in this case. 
Go into this directory.

```commandline
cp -r  /ihome/crc/how_to_run/ansys/CFX/SMP CFX_example

cd CFX_example
```

Adjust the parameters within the template job script `smp_ansys_cfx.slurm` according to your own case such as the name 
of your input file, time allocated on the cluster and the number cores, etc. 

This is the content of the template script, which should be modified according to your needs:

```shell
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=12
#SBATCH --cluster=smp
#SBATCH --partition=smp
#SBATCH --output=AnsysJob.o%j
#SBATCH --error=AnsysError.e%j
#SBATCH --time=1:00:00
#SBATCH --job-name=ke

# Load Modules
module load ansys

unset SLURM_GTIDS

# Executable
CFX_EXE=cfx5solve

# Input files
DEF_FILE=ke.def

numcores=$SLURM_NTASKS

# Launch CFX
$CFX_EXE -def $DEF_FILE -par-local -partition $numcores
```
Submit the job by running `sbatch smp_ansys_cfx.slurm`. You can check the full status of your job on the cluster by 
running the wrapper `crc-squeue.py`, as follows:

```commandline
[chx33@login1 CFX_example]$ crc-squeue.py
CLUSTER: gpu
  JOBID PAR                                NAME ST         TIME  NODES CPUS     NODELIST(REASON)

CLUSTER: htc
  JOBID PAR                                NAME ST         TIME  NODES CPUS     NODELIST(REASON)

CLUSTER: invest
  JOBID PAR                                NAME ST         TIME  NODES CPUS     NODELIST(REASON)

CLUSTER: mpi
  JOBID PAR                                NAME ST         TIME  NODES CPUS     NODELIST(REASON)

CLUSTER: smp
  JOBID PAR                                NAME ST         TIME  NODES CPUS     NODELIST(REASON)
7642841 smp                                  ke  R         0:08      1   12              smp-n29
```

You have now successfully submitted your first ANSYS CFX job. The outputs of your job, including potential error 
messages, are written into the same directory as your job script, which you can check as below:

```commandline
[chx33@login1 CFX_example]$ ls
AnsysError.e7642841  AnsysJob.o7642841  ke_001.out  ke_001.res  ke_002.out  ke_002.res  ke.def  smp_ansys_cfx.slurm
```

More examples and templates for ANSYS can be found in the folder `/ihome/crc/how_to_run/ansys`. 
Good luck with your class and projects!

!!! note

    You can only access the license server directly from a node or portal on the CRC cluster, as described above. 
    If you need to connect directly from your desktop computer,  please submit a [request](https://pittforms.tfaforms.net/f/PittITServiceRequest) 
    and **specify in your ticket that the case be transferred to SSOE IT**. You will need to provide the following 
    information in your request:

    - Software Name:
    - Request date:
    - Department:
    - P.I.:
    - P.I. Email:
    - User:
    - Email:
    - Computer Name:
    - IP Address:
    - in Pitt’s Active Directory (Y/N):

## Checking the License Status

The CRC is licensed to use ANSYS but the number of licenses is not particularly high for certain tools.
The licenses also operate on a per-node basis, meaning two jobs by the same user submitted to different compute nodes 
will use up a license until those jobs are completed. Please be considerate of other users when submitting long jobs. 
Also, if you use VIZ to access ANSYS with a GUI, please make sure your session is ended properly when you are done so 
that others may use it. 

You may receive an error message if all the licenses are in use.

For detailed information about all licenses currently in use, visit the 
[following link](https://ssoe-vlic-09.engr.pitt.edu/details.php?listing=0&amp;server=11) to check the status of the 
license server (you must be logged in to the VPN via GlobalProtect).

The page displays license information for all ANSYS products. lIf you know the name of the functionality you want to 
use, you can search for it within the page on your browser.

If the particular tool you want to use does not have available licenses for you to use, you will need to wait until 
another user's jobs finish. 

 

With a username from the license file, you can check the remaining time on their jobs with squeue. For example, 
let's look at the user jre31 from the license server output above:

```commandline
​ jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1334), start Thu 2/10 15:05
```

Their username is `jre31`, and their job is running on a compute node in the smp cluster (`smp-n162`). 

```commandline
squeue -M smp -u jre31 -o "%.18i %.9P %.8j %.8u %.2t %.10M %.6D %.20R %L"
```

will show their jobs on that cluster, and providing the `%L` argument to the format of the output of that function 
shows the `TIME_LEFT (D-HH:MM:SS)` given how long the job has been running and the QOS they defined for the job. 
