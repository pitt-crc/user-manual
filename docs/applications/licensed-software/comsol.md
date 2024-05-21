# COMSOL Multiphysics

This page contains info aimed at first time users of COMSOL on CRC, but may also be useful to more experienced users. 

##Available Modules

COMSOL Multiphysics is a suite of engineering and scientific simulation tools. The current version available on the 
cluster is COMSOL 6.1. Please be aware that newer COMSOL files are not backward compatible with older versions. Below 
is a list of specialized add-ons that the CRC is licensed to use:

### Electromagnetics:

AC/DC, RF, and Semiconductor 

### Structural Mechanics & Acoustics:

Acoustic, MEMS, Structural Mechanics

### Multipurpose Products

Particle Tracing

### Fluid Flow & Heat Transfer Modules

CFD, Heat Transfer, Porous Media Flow

### Chemical Engineering Modules

Battery Design, Chemical Reaction Engineering, Corrosion, Electrodeposition, Electrochemistry, Fuel Cell & Electrolyzer

### Interfacing Products

CAD Import Module, Design Module, Matlab/Simulink

## Access modalities for COMSOL

### 1- Using graphic user interface (VIZ)

 Please read the documentation on crc.pitt.edu/viz on how to access VIZ and then watch the video below:

<iframe allow="autoplay" allowfullscreen="" height="405" src="https://pitt.hosted.panopto.com/Panopto/Pages/Embed.aspx?id=f4517c0d-6e39-4367-acd7-ada50152a8c4&amp;autoplay=false&amp;offerviewer=true&amp;showtitle=true&amp;showbrand=false&amp;captions=false&amp;interactivity=all" style="border: 1px solid #464646;" width="720"></iframe>

### 2- Using command line interface

<ul>
	<li><a href="https://crc.pitt.edu/user-support/resource-documentation/vpn-and-accessing-clusters">Login to the cluster</a></li>
	<li>Copy a template job from <span style="font-family:courier new,courier,monospace;">/ihome/crc/how_to_run/comsol/bed_reactor</span> to your own directory.</li>
	<li>Submit the job by running  <span style="font-family:courier new,courier,monospace;">sbatch smp.slurm</span>.</li>
	<li>Adjust this job to correspond to your use case</li>
</ul>

 

!!! note

    If you are using the same Slurm job submission script for your own simulation, all you have to do is to change 
    the name of your input file and the number of nodes, cores, etc. 

!!! note

    If you want to run a COMSOL simulation on the `mpi` cluster, look at this template: 
    `/ihome/crc/how_to_run/comsol/micromixer`

!!! note
    
    If you are using COMSOL for teaching purposes (for a course), you need to load the teaching module:

    ```commandline
    [shs159@login0 bed_reactor]$ module load comsol/5.6-teaching
    [shs159@login0 bed_reactor]$ ml

    Currently Loaded Modules:
      1) comsol/5.6-teaching

    [shs159@login0 bed_reactor]$
    ```

## Sample script

```shell
#!/bin/bash -l

#SBATCH --job-name=comsol_micromix
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=4
#SBATCH --time=0-01:00:00       # Syntax is DD-HH:MM:SS
#SBATCH --cluster=smp           # Run crc-sinfo.py to get the cluster and partition details
#SBATCH --partition=smp

# Load Modules
module load comsol/5.6

# define input
inp=packed_bed_reactor_3d  # First input argument: Name of input without extention
ext=mph # We use the same naming scheme as the software default extention

##########################
#DO NOT TOUCH THE FOLLOWING
##########################
# define directories
submitdir=${SLURM_SUBMIT_DIR}

# run Comsol
comsol batch -nn ${SLURM_NNODES}\
             -np ${SLURM_NTASKS_PER_NODE}\
             -inputfile ${inp}.${ext}\
             -outputfile ${inp}_out.${ext}\
             -batch \
             -mlroot /ihome/crc/install/matlab/R2021a/\
             -batchlog logfile.log
```

You may now have submitted your first COMSOL job.

Good luck with your (MULTI)physics!


## Checking the License Status

The CRC is licensed to use COMSOL, but the number of licenses is not particularly high for certain tools.

The licenses also operate on a per-node basis, meaning two jobs by the same user submitted to different compute nodes 
will use up a license until those jobs are completed. Please be considerate of other users when submitting long jobs. 
Also, if you use VIZ to access COMSOL with a GUI, please make sure your session is ended properly when you are done so 
that others may use it.

When you load the COMSOL module, truncated version of the license output will appear.

```commandline
[nlc60@login0b ~]#module load comsol

CURRENT LICENSE AVAILABILITY OF COMSOL GUI:
Users of COMSOLGUI:  (Total of 5 licenses issued;  Total of 2 licenses in use)
  "COMSOLGUI" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
[nlc60@login0b comsol] : module list

Currently Loaded Modules:
  1) comsol/5.6
```

This shows the availability of COMSOL GUI for users attempting to open COMSOL on VIZ.

You may see an error like this if all the GUI licenses are in use:

![](https://crc.pitt.edu/sites/default/files/pictures/COMSOL%20License%20Issues.png)

For the full license information output, open 
[the SSOE license server](https://ssoe-vlic-09.engr.pitt.edu/details.php?listing=0&amp;server=9) 
(which is behind the Pitt firewall just as the cluster) 

```commandline
CURRENT LICENSE AVAILIBILITY FOR COMSOL:
lmutil - Copyright (c) 1989-2021 Flexera. All Rights Reserved.
Flexible License Manager status on Thu 2/10/2022 17:21

License server status: 4800@ssoe-vlic-07.engr.pitt.edu
    License file(s) on ssoe-vlic-07.engr.pitt.edu: C:\Program Files\COMSOL\COMSOL60\Multiphysics\license\license.dat:

ssoe-vlic-07.engr.pitt.edu: license server UP (MASTER) v11.18.1

Vendor daemon status (on ssoe-vlic-07.engr.pitt.edu):

  LMCOMSOL: UP v11.18.1
Feature usage info:

Users of PERMANENT:  (Uncounted, node-locked)

Users of SERIAL:  (Uncounted, node-locked)

  "SERIAL" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  vendor_string: Y,57F1198202190D83
  uncounted nodelocked license locked to NOTHING (hostid=ANY)

    bam184 viz-n1.crc.pitt.edu /dev/pts/22 (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1408), start Mon 2/7 11:59
    jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 688), start Thu 2/10 15:05

Users of ACDC:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of ACDCBATCH:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of ACOUSTICS:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of ACOUSTICSBATCH:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of BATTERYDESIGN:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of BATTERYDESIGNBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of CADIMPORT:  (Total of 4 licenses issued;  Total of 0 licenses in use)

Users of CADIMPORTBATCH:  (Total of 4 licenses issued;  Total of 0 licenses in use)

Users of CFD:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of CFDBATCH:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of CHEM:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of CHEMBATCH:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of COMSOL:  (Total of 5 licenses issued;  Total of 2 licenses in use)

  "COMSOL" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  floating license

    bam184 viz-n1.crc.pitt.edu /dev/pts/22 (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 943), start Mon 2/7 11:59
    jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 353), start Thu 2/10 15:05

Users of COMSOLBATCH:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of CORROSION:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of CORROSIONBATCH:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of DESIGN:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of DESIGNBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of ELECTROCHEMISTRY:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of ELECTROCHEMISTRYBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of ELECTRODEPOSITION:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of ELECTRODEPOSITIONBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of FUELCELLANDELECTROLYZER:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of FUELCELLANDELECTROLYZERBATCH:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of HEATTRANSFER:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of HEATTRANSFERBATCH:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of LLMATLAB:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of LLMATLABBATCH:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of MEMS:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of MEMSBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of PARTICLETRACING:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of PARTICLETRACINGBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of POROUSMEDIAFLOW:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of POROUSMEDIAFLOWBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of RF:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of RFBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of SEMICONDUCTOR:  (Total of 2 licenses issued;  Total of 2 licenses in use)

  "SEMICONDUCTOR" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  floating license

    bam184 viz-n1.crc.pitt.edu /dev/pts/22 (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1515), start Wed 2/9 10:18
    jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 2321), start Thu 2/10 15:05

Users of SEMICONDUCTORBATCH:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of STRUCTURALMECHANICS:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of STRUCTURALMECHANICSBATCH:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of COMSOLGUI:  (Total of 5 licenses issued;  Total of 1 license in use)

  "COMSOLGUI" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  floating license

    bam184 viz-n1.crc.pitt.edu /dev/pts/22 (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 213), start Mon 2/7 11:59

Users of CLIENTSERVER:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of CLUSTERNODE:  (Total of 5 licenses issued;  Total of 1 license in use)

  "CLUSTERNODE" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  floating license

    jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1228), start Thu 2/10 15:05

Users of COMSOLUSER:  (Total of 5 licenses issued;  Total of 2 licenses in use)

  "COMSOLUSER" v6.0, vendor: LMCOMSOL, expiry: 1-oct-2022
  floating license

    bam184 viz-n1.crc.pitt.edu /dev/pts/22 (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 115), start Mon 2/7 11:59
    jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1334), start Thu 2/10 15:05

Users of MODELMANAGER:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of MODELMANAGERBATCH:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of MODELMANAGERSERVER:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of MODELMANAGERSERVERBATCH:  (Total of 5 licenses issued;  Total of 0 licenses in use)

Users of CADREADER:  (Total of 4 licenses issued;  Total of 0 licenses in use)

Users of CADREADERBATCH:  (Total of 4 licenses issued;  Total of 0 licenses in use)

Users of CADIMPORTUSER:  (Total of 4 licenses issued;  Total of 0 licenses in use)

Users of BATTERIESANDFUELCELLS:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of BATTERIESANDFUELCELLSBATCH:  (Total of 1 license issued;  Total of 0 licenses in use)

Users of ACO:  (Total of 2 licenses issued;  Total of 0 licenses in use)

Users of HT:  (Total of 3 licenses issued;  Total of 0 licenses in use)

Users of SME:  (Total of 3 licenses issued;  Total of 0 licenses in use)
```

If you know the name of the functionality you want to use, you can pipe the output to grep for a more concise output.

If the particular tool you want to use does not have available licenses for you to use, you will need to wait until 
another user's jobs finish. 


With a username from the license file, you can check the remaining time on their jobs with squeue. For example, let's 
look at the user jre31 from the license server output above:

```commandline
​ jre31 smp-n162.crc.pitt.edu /dev/tty (v5.6) (ssoe-vlic-07.engr.pitt.edu/4800 1334), start Thu 2/10 15:05
```

Their username is `jre31`, and their job is running on a compute node in the `smp` cluster (smp-n162). 

```commandline
squeue -M smp -u jre31 -o "%.18i %.9P %.8j %.8u %.2t %.10M %.6D %.20R %L"
```

will show their jobs on that cluster, and providing the `%L` argument to the format of the output of that function 
shows the `TIME_LEFT (D-HH:MM:SS)` given how long the job has been running and the QOS they defined for the job. 


### 3- Connect from your local computer

You can also run COMSOL from your local machine and check out the license from the SSOE server. All you need to do is to specify the license server hostname and port number during step 3 of the COMSOL installation on your machine, as described in the COMSOL [documentation](https://doc.comsol.com/6.2/doc/com.comsol.help.comsol/comsol_installation.02.012.html). The server hostname should be ssoe-vlic-07.engr.pitt.edu, and port number is 4800, just as shown above. You would also need to be connected to Pitt VPN via Global Protect, same way as you would connect to VIZ or other CRC portals. Before you can connect to the server, you need also to submit a [request](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=237&RequestorType=Service?) to the SSOE IT team to add your computer to the server.
*Specify in your ticket that the case be transferred to SSOE IT*. You will need to be logged in to your Pitt account and provide the following information in your request:

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
