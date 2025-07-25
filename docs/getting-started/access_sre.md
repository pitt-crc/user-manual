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

A schematic of the process is depicted below.

![GETTING-STARTED-MAP](../_assets/img/avd/schematic-SRE.png)

^^**Definitions**^^

*   **Client** -- this is your computer or internet-connected device
*   **Access Portal** -- one of several remote servers used to submit jobs to the high performance computing clusters or to perform
data management operations
*   **CRCD SRE** -- the total footprint of the CRCD Secure Research Environment, including a high performance computing 
cluster, a data storage system, access portals, networking equipment, and software 
*   **Azure Virtual Desktop** -- A cloud-based Windows Remote Desktop that has a direct connection to the CRCD SRE 

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

        Shown below is the Desktop of the CRCD-SRE AVD, where there are active connections
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

##**5. ^^Data Ingress Method^^**

During onboarding to the CRCD_SRE, members of the Research Team with responsibility for the data security will be referred 
to as Data Managers in this environment. These individuals are provided with access to the Azure Storage Account for the 
Research Project where a data ingestion automation works to assist with moving data into the CRCD_SRE storage.

A GLOBUS Storage Gateway and GLOBUS Collection were configured to allow Data Managers to easily transfer files from other 
storage platforms into the CRCD_SRE environment. The GLOBUS Collection for your Research Projects will have the Data Use 
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

Data transfer from Azure to CRCD_SRE storage will begin once the first transfer succeeds. Once all transfers are complete, the 
only copy of the data remaining exists on the Source and the CRCD_SRE storage. The Azure Storage Account does not retain a copy of the data.

The data will arrive in the CRCD_SRE storage filesystem for the Research Project associated with the Data Use Agreement under the /ingress directory.

<!--- ![sre_ingress-globus.5](../_assets/img/avd/crcd-sre_ingress-globus.5.png) --->

##**6. ^^Data Egress Method^^**

During onboarding to the CRCD_SRE, members of the Research Team with responsibility for the data security will be referred to as 
Data Managers in this environment. 

These individuals are responsible to reviewing and approving any requests for data moving out of the secure environment. When a 
Research Team member stages a file for egress approval, the Data Managers will receive an email with the available information about 
the file. To prevent accidental sharing of sensitive data within the request, the Data Manager(s) will need to review the file 
on the CRCD_SRE filesystem. 

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

##**7. ^^Ending your AVD session^^**

Once you are done with your work session, be sure to Sign out.

![MacOS-AVD-10-HIPAA](../_assets/img/avd/MacOS-AVD-10-SRE.png)
