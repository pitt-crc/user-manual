---
hide:
  - toc
---

# Access CRCD SRE via Azure Virtual Desktop (AVD)

The CRCD Secure Research Environment (SRE) is where you can do computation on [**regulated data**](https://www.technology.pitt.edu/security/data-risk-classification-and-compliance-operating-standard)
requiring NIST SP 800-171 compliance (self-attested) and is suitable for satisfying controlled-access data requirements from NSF and NIH. It is suitable for 
storing data containing Protected Health Information (PHI),
particularly data containing any of the 18 [**HIPAA identifiers**](https://www.hrpo.pitt.edu/hipaa-safe-harbor-vs-limited-data-set). This environment is restricted
to authorized users on projects that are covered under an approved IRB application or data use agreement (DUA). The documentation below describes how to set up AVD for accessing the 
CRCD SRE.

A schematic of the process is depicted below. The AVD is the user portal from which you can access the CRCD SRE login node (`login.res.crc.pitt.edu`) 
or the Open OnDemand URL (`https://ondemand.res.crc.pitt.edu`).

![GETTING-STARTED-MAP](../_assets/img/avd/schematic-SRE.png)

^^**Definitions**^^

*   **Client** -- this is your computer or internet-connected device
*   **Access Portal** -- one of several remote servers used to submit jobs to the high performance computing clusters or to perform
data management operations
*   **CRCD SRE** -- the total footprint of the CRCD Secure Research Environment, including a high performance computing 
cluster, a data storage system, access portals, networking equipment, and software 
*   **Azure Virtual Desktop** -- A cloud-based Windows Remote Desktop that has a direct connection to the CRCD SRE 

^^**Hardware Profile**^^

| Partition | Host Architecture | Nodes | Cores/Node | Mem/Node | Mem/Core | Scratch | Network | Node Names |
|-----------|-------------------|-------|------------|----------|----------|---------|---------|------------|
| cpu       | [AMD EPYC 9374F (Genoa)](https://www.amd.com/en/products/processors/server/epyc/4th-generation-9004-and-8004-series/amd-epyc-9374f.html)                                       | 5    | 64         | 768 GB   | 12 GB    | 3.2 TB NVMe | 10GbE   | n\[0-4]    |

| Partition | GPU Type | Nodes | GPU/Node  | Host Architecture | Core/Node  | Max Core/GPU  | Mem/Node   | Mem/Core  | Scratch    | Network  | Node Names    |
|-----------|------------|----------|-----------|-------------------|------------|---------------|------------|-----------|------------|----------|---------------|
| gpu       | [L40S (48GB)](https://www.nvidia.com/en-us/data-center/l40s/) |    2   | 4         | [Intel Xeon Platinum 8462Y+](https://www.intel.com/content/www/us/en/products/sku/232383/intel-xeon-platinum-8462y-processor-60m-cache-2-80-ghz/specifications.html)          | 64          | 16            | 512 GB     | 8 GB      | 7 TB NVMe  | 10GbE             | gpu-n\[0-1] |

##**1. ^^Install and Configure Remote Desktop client^^**

You will need to install the [Remote Desktop client](https://learn.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) 
for your OS. The previous link provides instructions for various types of devices. Below, we will only highlight MacOS and Windows. 

!!! example "Installing and Configuring the Remote Desktop client"
    === "MacOS"
        In MacOS, the Windows App client software is distributed through the Mac App Store. Open the the Windows App after download.

        ![MacOS-AVD-01](../_assets/img/avd/MacOS-AVD-01.png)

        Next, add Pitt's Remote Desktop Device by selecting *Add Work or School Account* from the + widget, located towards the upper right-hand 
        corner of the window.

        ![MacOS-AVD-02](../_assets/img/avd/MacOS-AVD-02.png)

        This will take you to the *Microsoft Sign in* panel for authenticating using Pitt Single Sign-On.

        | Authenticate via Pitt Passport| |
        | ----------- | ------------------------------------ |
        | **1**![MacOS-AVD-03](../_assets/img/avd/MacOS-AVD-03.png) | **2**![MacOS-AVD-04](../_assets/img/avd/MacOS-AVD-04.png) |
        | **3**![MacOS-AVD-05](../_assets/img/avd/MacOS-AVD-05.png) | **4**![MacOS-AVD-06](../_assets/img/avd/MacOS-AVD-06.png) |

    === "Windows"

        In Windows, the Microsoft Remote Desktop software is distributed through the Microsoft App Store. Open Remote Desktop installer after download.
        ![Win-AVD-01](../_assets/img/avd/Win-AVD-01.png)

        Next, add Pitt's Workspaces by selecting *Workspaces* from the **+ Add** widget, located towards the upper right-hand
        corner of the window.

        ![Win-AVD-02](../_assets/img/avd/Win-AVD-02.png)

        This will take you to the *Subscribe to a Workspace* window where you will be asked to sign in using your Pitt credentials..

        ![Win-AVD-03](../_assets/img/avd/Win-AVD-03.png)

        | Authenticate via Pitt Passport| |
        | ----------- | ------------------------------------ |
        | **1**![Win-AVD-04](../_assets/img/avd/Win-AVD-04.png) | **2**![Win-AVD-05](../_assets/img/avd/Win-AVD-05.png) |
        | **3**![Win-AVD-06](../_assets/img/avd/Win-AVD-06.png) | **4**![Win-AVD-07](../_assets/img/avd/Win-AVD-07.png) |

##**2. ^^Connecting to an AVD Device^^**

!!! example "Connecting to a Remote Device"
    === "MacOS"

        After successful authentication, you will be presented with list of authorized remote Devices that you can connect to. Your
        device list may be different from what is shown below, depending on your role. Authorized users of the Secure Research Environment will
        see a *Device* called **CRCD-SRE**. 
        If you do not see this *Device* but should have access, please submit a
        [**help ticket**](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service),
        stating that you need authorization to use AVD to access the CRCD Secure Research Environment.

        ![MacOS-AVD-07-HIPAA](../_assets/img/avd/MacOS-AVD-07-SRE.png)

        Double clicking on that selection will prompt for your Pitt credentials.

        ![MacOS-AVD-08-HIPAA](../_assets/img/avd/MacOS-AVD-08-SRE.png)

        You will see the Remote Windows Desktop after successful login. From this remote portal, you can access the Secure Research Environment.

        ![MacOS-AVD-09-HIPAA](../_assets/img/avd/MacOS-AVD-09-SRE.png)

    === "Windows"

        After successful authentication, you will be presented with list of authorized Workspaces that you can connect to. Your
        Workspaces may be different from what is shown below, depending on your role. Authorized users of the HIPAA environment will
        see a *Workspace* called **CRCD-SRE**.
        If you do not see this *Workspace* but should have access, please submit a
        [**help ticket**](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service),
        stating that you need authorization to use AVD to access the CRCD Secure Research Environment.

        ![Win-AVD-09-HIPAA](../_assets/img/avd/Win-AVD-09-SRE.png)

        Double clicking on that selection will prompt for your Pitt credentials.

        | Authenticate via Pitt Passport| |
        | ----------- | ------------------------------------ |
        | **1**![Win-AVD-10-HIPAA](../_assets/img/avd/Win-AVD-10-HIPAA.png) | **2**![Win-AVD-11-HIPAA](../_assets/img/avd/Win-AVD-11-HIPAA.png) |

        You will see the Remote Windows Desktop after successful login. From this remote portal, you can access the HIPAA environment.

        ![Win-AVD-12-HIPAA](../_assets/img/avd/MacOS-AVD-09-SRE.png)

##**3. ^^Various Methods Connecting to CRCD SRE^^**

!!! example "Options for Connecting to CRCD SRE"

    === "Full Desktop Overview of all Tools"

        Shown below is the Desktop of the CRCD SRE AVD, where there are active connections
        to CRCD using

        * **PuTTY**
        * **Open OnDemand portal via a web browser**

        ![MacOS-AVD-All-Tools](../_assets/img/avd/MacOS-AVD-All-Tools-SRE.png)

        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>

    === "PuTTY"
        Search for and launch the PuTTY app from the Windows Start Menu.

        ![MacOS-AVD-PuTTY-01](../_assets/img/avd/MacOS-AVD-PuTTY-01-SRE.png)

        Fill in the PuTTY Configuration using the following values:

        * **Host Name (or IP address):** login.res.crc.pitt.edu
        * **Port:** 22
        * **Connection type:** SSH

        You might also want to Save the profile under a name for quick loading in the future.

        ![MacOS-AVD-PuTTY-02](../_assets/img/avd/MacOS-AVD-PuTTY-02-SRE.png)

        When you first connect, you may see the following PuTTY Security Alert message below. Select Accept.

        ![MacOS-AVD-PuTTY-03](../_assets/img/avd/MacOS-AVD-PuTTY-03-SRE.png)

        The login credentials are your  Pitt username (all lowercase) and password.

        ![MacOS-AVD-PuTTY-04](../_assets/img/avd/MacOS-AVD-PuTTY-04-HIPAA.png)

        A successful authentication will present you with a terminal to the CRCD SRE login node.

        ![MacOS-AVD-PuTTY-05](../_assets/img/avd/MacOS-AVD-PuTTY-05-SRE.png)

    === "CRCD Webportals"

        From within the AVD, all CRCD webportals are accessible, including

        * **Open OnDemand:** [https://ondemand.res.crc.pitt.edu](https://ondemand.res.crc.pitt.edu)
  
        ![MacOS-AVD-Webportals-01](../_assets/img/avd/MacOS-AVD-Webportals-01-SRE.png)

        ![MacOS-AVD-Webportals-02](../_assets/img/avd/MacOS-AVD-Webportals-02-SRE.png)

        ![MacOS-AVD-Webportals-03](../_assets/img/avd/MacOS-AVD-Webportals-03-SRE.png)

##**4. ^^Recommendations on setting up your software environment^^**

The CRCD team will work with you to set up the software environment. Where self service is possible, we will point
you to the appropriate documentation that shows you how to do this yourself. If you run into problems, please 
submit a [**help ticket**](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and we will engage in finding the solution. We summarize below, the best practices for various software.

*   **R and RStudio** -- Most users will already have a working environment and will want to transfer 
packages from there to the CRCD SRE. Here is a 
[**blog post**](https://www.r-bloggers.com/2017/07/quick-way-of-installing-all-your-old-r-libraries-on-a-new-device/) that
shows how to automate the package installation process in three steps.

*   **Python** -- The CRCD provides [**guidance on setting up a Python environment**](../applications/python.md). One
way clone an environment from one system to another is to create a requirements file and then to use that for the 
package installation in the other system. This is described in 
the [**pip documentation**](https://pip.pypa.io/en/latest/user_guide/#requirements-files).

Similar to the Open Science Environment, the CRCD SRE uses the [**Lmod**](https://github.com/TACC/Lmod) system to manage and 
provision software. Please refer to the [**How to Discover Available Software**](step3/getting-started-step3-software.md) 
section for descriptions about the `module` commands.

##**5. ^^Job Submission Through Slurm^^**

The CRCD SRE uses [**Slurm**](https://slurm.schedmd.com/quickstart.html) to manage the computing resources. Please refer 
to the sections [**How to Request Computing Resources**](step3/getting-started-step3-resources.md) and 
[**How to Manage Computing Jobs**](step3/getting-started-step3-manage-jobs.md) for the standard Slurm commands. Unlike the
CRCD Open Science Environment, which has many clusters and many partitions, the SRE only has one cluster and a `cpu` and `gpu`
partition (please refer to the Hardware Profile section at the top).

![sre_sinfo](../_assets/img/avd/SRE_sinfo.png)

You target a specific partition by requesting it using the `--partition` Slurm directive. For example, to request an 
interactive session for 24 hours with 32 CPU cores, you can use the following `srun` command:

```commandline
srun --export=ALL --partition=cpu --nodes=1 --ntasks-per-node=32 --time=24:00:00 --pty bash
```
The default value for `--nodes` is 1, so you can leave that out of the command and it still works

![sre_cpu](../_assets/img/avd/SRE_cpu.png)

To target the GPUs, you ask for it with the `--partition=gpu` Slurm directive. You can leave off the `--nodes` and even
the `--ntasks-per-node` directives and only specify the number of GPUs with the `--gres=gpu:<num_GPUs>` (1) and Slurm
will provision the desired resources. Each requested GPU will be provisioned the corresponding **Max Cores/GPU**. The 
graphic below shows several examples: 
{ .annotate }

1.  Throughout the examples, we use the conventional syntax `<variable>` to represent a placeholder for an expected value that the user
    will provide.

![sre_gpu](../_assets/img/avd/SRE_gpu.png)

Here's another example requesting four GPUs and showing the output from the `nvidia-smi` command:

![sre_gpu](../_assets/img/avd/SRE_gpu-4.png)

To craft a **Slurm batch script**, you take the above directives and format it with the `#SBATCH` prefix. In
the example below, we provide the **Whole Script** on the first panel and then break it down into the constituent 
parts in subsequent panels.

!!! example "Architecture of a Slurm job submission script"
    === "Whole Script"
        ```bash
        #!/usr/bin/env bash

        ## ------------------------------------------------------------------
        ## Slurm directives defining the resource request
        ## ------------------------------------------------------------------
        #SBATCH --job-name=sre-gpu
        #SBATCH --output=sre-gpu.out
        #SBATCH --nodes=1
        #SBATCH --ntasks-per-node=16
        #SBATCH --partition=gpu
        #SBATCH --gres=gpu:1
        #SBATCH --time=24:00:00

        ## ---------------------------------------------------------------------
        ## Load software into environment
        ## ---------------------------------------------------------------------
        module purge
        module load gcc/10.2.0  openmpi/4.1.1
        module load amber/24

        ## ---------------------------------------------------------------------
        ## Setup software execution environment
        ## ---------------------------------------------------------------------
        # Define environmental variables for Amber input/output files
        INP=md.in
        TOP=mocvnhlysm.top
        CRD=mocvnhlysm.crd
        OUT=mocvnhlysm


        # Define software executable
        SANDER=pmemd.cuda

        # Display environmental variables to Slurm output file for diagnostics
        echo AMBERHOME    $AMBERHOME
        echo SLURM_NTASKS $SLURM_NTASKS
        echo which SANDER `which $SANDER`
        echo "Running on node:" `hostname`

        # Display NVIDIA GPU information to Slurm output file
        nvidia-smi

        # Software execution line
        $SANDER  -O     -i   $INP   -p   $TOP   -c   $CRD   -r   $OUT.rst \
                        -o   $OUT.out   -e   $OUT.ene   -v   $OUT.vel   -inf $OUT.nfo   -x   $OUT.mdcrd
        ```
        !!! info
            The job script should start the first line with the standard [**shebang**](https://en.wikipedia.org/wiki/Shebang_(Unix))
            line, specifying the [**Linux shell**](https://en.wikipedia.org/wiki/Unix_shell).
    === "Part1: Slurm Directives"
        ```bash
        ## ------------------------------------------------------------------
        ## Slurm directives defining the resource request
        ## ------------------------------------------------------------------
        #SBATCH --job-name=sre-gpu
        #SBATCH --output=sre-gpu.out
        #SBATCH --nodes=1
        #SBATCH --ntasks-per-node=16
        #SBATCH --partition=gpu
        #SBATCH --gres=gpu:1
        #SBATCH --time=24:00:00
        ```
        The syntax for a Slurm directive begins with `#SBATCH` followed by ` --<variable>=<value>`, where `<variable>`
        is one of the OPTIONS defined for the [**sbatch**](https://slurm.schedmd.com/sbatch.html) command. The specific
        `<value>` is unique to how CRCD configures our implementation of the Slurm Workload Manager and is provided in
        <insert link to table>.

        !!! info
            Most if not all [**Linux shells**](https://en.wikipedia.org/wiki/Unix_shell) interpret a line starting with
            the `#` sign as a comment, meaning that the line is
            not executed as a command. If you were to execute the above section of text as a `bash` script, the text is
            treated as comments only. However, if you were to run the above section of text as input to the Slurm `sbatch` command,
            `sbatch` will treat every instance of `#SBATCH` as a command to it (hence, the term *directive*) and will interpret
            the ` --<variable>=<value>` parameters accordingly. Any other combination of text starting with a `#` sign will
            be treated as a comment, such as the `## ` syntax in the example above.

        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
    === "Part2: Load Software"
        ```bash
        ## ---------------------------------------------------------------------
        ## Load software into environment
        ## ---------------------------------------------------------------------
        module purge
        module load gcc/10.2.0  openmpi/4.1.1
        module load amber/24
        ```

        !!! info
            We first purge all previously loaded software and only load the packages that are needed.  These commands will
            expose the installed software to the user environment by updating the `$PATH`, `$LD_LIBRARY_PATH`, and other required
            environmental variables as specified in the package installation instructions.

        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>

    === "Part3: Run Software"
        ```bash
        ## ---------------------------------------------------------------------
        ## Setup software execution environment
        ## ---------------------------------------------------------------------
        # Define environmental variables for Amber input/output files
        INP=md.in
        TOP=mocvnhlysm.top
        CRD=mocvnhlysm.crd
        OUT=mocvnhlysm

        # Define software executable
        SANDER=pmemd.cuda

        # Display environmental variables to Slurm output file for diagnostics
        echo AMBERHOME    $AMBERHOME
        echo SLURM_NTASKS $SLURM_NTASKS
        echo which SANDER `which $SANDER`
        echo "Running on node:" `hostname`

        # Display NVIDIA GPU information to Slurm output file
        nvidia-smi

        # Software execution line
        $SANDER  -O     -i   $INP   -p   $TOP   -c   $CRD   -r   $OUT.rst \
                        -o   $OUT.out   -e   $OUT.ene   -v   $OUT.vel   -inf $OUT.nfo   -x   $OUT.mdcrd
        ```

        !!! info
            This section contains the software specific setup and execution line(s). Typically, you should be able to copy
            over the commands that you use when running on your local laptop or desktop, with some modifications. At the most
            straightforward level, you should be able to translate the setup/commands described in the software user manual to
            work within the CRCD Ecosystem.

        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>

##**6. ^^Data Ingress Method^^**

During onboarding to the CRCD SRE, members of the Research Team with responsibility for the data security will be referred 
to as Data Managers in this environment. These individuals are provided with access to the Azure Storage Account for the 
Research Project where a data ingestion automation works to assist with moving data into the CRCD SRE storage.

A GLOBUS Storage Gateway and GLOBUS Collection were configured to allow Data Managers to easily transfer files from other 
storage platforms into the CRCD SRE environment. The GLOBUS Collection for your Research Projects will have the Data Use 
Agreement # in the Collection title.

![sre_ingress-globus.1](../_assets/img/avd/crcd-sre_ingress-globus.1.png)

Data Manager(s) can use the GLOBUS Web Client to access existing storage they use that has been configured with a GLOBUS 
Collection. Utilizing the two-pane view in GLOBUS Web, for instance, allows you to also bring up another storage location 
(Collection) in a side-by-side view - making it easy to navigate to the files you wish to ingress on one side of the screen, 
and simply drag/drop or transfer in bulk to the GLOBUS Collection associated with the Research Projects Storage Account.

<!--- ![sre_ingress-globus.2](../_assets/img/avd/crcd-sre_ingress-globus.2.png) --->

![sre_ingress-globus.3](../_assets/img/avd/crcd-sre_ingress-globus.3.png)

The data transfer from Source -> Azure will be handled by GLOBUS, and you should receive a confirmation email for the transfer.

![sre_ingress-globus.4](../_assets/img/avd/crcd-sre_ingress-globus.4.png)

Data transfer from Azure to CRCD SRE storage will begin once the first transfer succeeds. Once all transfers are complete, the 
only copy of the data remaining exists on the Source and the CRCD SRE storage. The Azure Storage Account does not retain a copy of the data.

The data will arrive in the CRCD SRE storage filesystem for the Research Project associated with the Data Use Agreement under the /ingress directory.

<!--- ![sre_ingress-globus.5](../_assets/img/avd/crcd-sre_ingress-globus.5.png) --->

##**7. ^^Data Egress Method^^**

During onboarding to the CRCD SRE, members of the Research Team with responsibility for the data security will be referred to as 
Data Managers in this environment. 

These individuals are responsible to reviewing and approving any requests for data moving out of the secure environment. When a 
Research Team member stages a file for egress approval, the Data Managers will receive an email with the available information about 
the file. To prevent accidental sharing of sensitive data within the request, the Data Manager(s) will need to review the file 
on the CRCD SRE filesystem. 

Moving data out of the CRCD SRE requires approval from the Data Manager. Each project will have one or more designated Data Manager(s) whose
role is to ensure that none of the sensitive data leaves the SRE, while facilitating egress of non-sensitive analysis or simulation results 
for publishing. Each project will have a designated **egress** directory. Copying file(s) into the egress directory will trigger a workflow
that will

**A.** Send an email to the Data Manager with an option to Approve or Deny the request. It is the responsibility of the Data Manager to log in 
to the AVD to verify that all sensitive data have been sanitized from the file before approving the request.

![sre_egress_email](../_assets/img/avd/crcd_sre_egress_email.png)

**B.** Selecting **Approve** will trigger a workflow that will transfer the file to a designated Azure Blob storage directory from which the 
requester can download unto their client computer. After a successful transfer, the file in the **egress** directory will be deleted.

![approve](../_assets/img/avd/Az-Egress-Approval-Logic-App.png) 

Selecting **Deny** will delete the data from the **egress* directory and prevent any data transfer out of the SRE.

**C.** The Data Manager will also receive an email confirmation with a summary of the data transfer details. 
![approve_confirmation](../_assets/img/avd/approve_confirm.png)

##**8. ^^Ending your AVD session^^**

Once you are done with your work session, be sure to Sign out.

![MacOS-AVD-10-HIPAA](../_assets/img/avd/MacOS-AVD-10-SRE.png)
