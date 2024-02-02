# Lumerical Photonics Simulation Suite (ANSYS)

## 1.  Licensing setup on your own laptop or desktop

<span style="color:#FF0000;"><u>**Update**</u></span>: A newer 2023 version of Lumerical is now available 
and can be accessed via the module system with the command "module load lumerical/2023R1.2"

!!! note 

    You can only access the license server directly from a node or portal on the CRC cluster. 
    If you need to connect directly from your desktop computer, please submit 
    a [request](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=237&RequestorType=Service?) and **specify in your ticket that 
    the case be transferred to SSOE IT**. You will need to be logged in to your Pitt account and provide the following information in your request:

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

In order to check out tokens from the license server, you need to be on PittNet. What this means is that your computer 
either needs to be directly attached to an Ethernet port by wire or that you are on VPN over wireless. Please set up 
your client in the user interface as shown below. The name of the **new** license server 
is `ssoe-lic-ansys.engr.pitt.edu`, and the port number needs to be specified as `4810`. Old versions of Lumerical may 
not work with the latest license server, so you may need to update to the latest 
software (version 2023 R1, as of this writing).

![](https://crc.pitt.edu/sites/default/files/Lumerical_license.png)

Once this is configured, you should be able to invoke the Lumerical GUI.

![](https://crc.pitt.edu/sites/default/files/fdtd_license_checkout_success-1.png)

![](https://crc.pitt.edu/sites/default/files/fdtd_license_checkout_success-2.png)

## 2. Using the GUI on CRC visualization portal

CRC has deployed a web-based portal with visualization capabilities for accessing our compute and storage resources. 
More details can be obtained [here](../../web-portals/viz.md). Below is a summary of how to deploy the Lumerical 
launcher on `viz.crc.pitt.edu`. The prerequisites are (1) you are on PittNet or Pitt VPN, (2) you have an account and 
a compute allocation on CRC and (3) you are part of the `fdtd` group, indicating that you have permission to use this 
licensed software.

### Logging into the CRC visualization portal

Point your browser to viz.crc.pitt.edu and login using your Pitt credentials

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-01_0.png)

Launch a MATE session

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-03.png)

### Bringing up the Lumerical GUI

This steps above will launch a new tab in your browser containing a Linux desktop. From the toolbar at the top, click 
on the Terminal icon to open a terminal. Within the terminal, type

```commandline
module purge
module load lumerical
launcher
```

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-04.png)

This will bring up the familiar Ansys Lumerical Launcher from where we can configure the license checkout. 
Click on **Licenses** and select Ansys under the **License Manager Selection** option and then **Apply**. 
If you are prompted to **Restart Now**, go ahead and click that.

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-05.png)

When the Launcher windows comes back, click on **Licenses** again and use `license-clus.crc.pitt.edu` as the target 
value of the **Server**. This DNS name points to the same server as `license.crc.pitt.edu` but using an interface 
attached to our internal network. If prompted to **Restart Now**, choose that.

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-06.png)

Once licensing is set up, you can launch the Lumerical IDE.

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-07.png)

I checked using `nvidia-smi` and the IDE is accelerated using the discrete graphics card.

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-08.png)

Remember to **EXIT** the IDE when done so that the license token gets returned to the pool for others to check out. 
Unless you **Shut Down** or **Log Out**, the MATE session will remain open, even if you close your browser. 
What this means is that you can start a job on `viz.crc.pitt.edu`, close your laptop and go to lunch, 
log back in later and continue where you left off. If your job was running in the background, it will continue to make 
progress during your lunchtime.

To end your MATE session, click on **System** followed by **Shut Down** or **Log Out**.

![](https://crc.pitt.edu/sites/default/files/viz-lumerical-09.png)

## 3. Submitting a Slurm batch job to CRC cluster

There is a limited number of GUI licenses (3x) but a larger number of compute engine licenses (12x). One strategy to 
optimize usage of the GUI licenses is to use the Lumerical IDE to set up the simulation system, close the IDE 
(to free up the license), and then submit jobs to the CRC cluster. You will need a FDTD project file (`.fsp`) and a 
Slurm job submission script. An example can be found within the following directory on the cluster login node

```commandline
/ihome/crc/how_to_run/lumerical
```

The job submission script `fdtd.slurm` is a text file

```shell
#!/bin/bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=16
#SBATCH --cluster=smp
#SBATCH --partition=smp
#SBATCH --time=72:00:00
#SBATCH --job-name=fdtd

# Load Modules
module purge
module load lumerical

# Run fdtd in batch mode

FDTD_MODEL=triangular_slit_100nm_width_6000nm_length.fsp

fdtd-engine-mpich2nem -t $SLURM_NTASKS $FDTD_MODEL
```

containing instructions for requesting resources (in this case, a compute node with 16 CPU cores for 72 hours), 
loading the software, and executing the software. This example job script can be easily adapted to your own use. 
The key changes will include updates to the resource list (values of the `#SBATCH` directives) and the value of the 
`FDTD_MODEL` variable, which is the FDTD Project file. To submit the job, execute the command

```commandline
sbatch fdtd.slurm
```

One important distinction about the compute engine licenses is worth noting: The license is based on CPU sockets. 
The CRC compute nodes have dual sockets (have two CPUs with each CPU having multiple cores). What that means is that 
each job will consume at most two compute engine licenses if the parallel job span both sockets to provide the 
requested `--ntasks-per-node`.
