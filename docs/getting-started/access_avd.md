---
hide:
  - toc
---

# Access CRC Using Azure Virtual Desktop (AVD)

If for whatever reason you are unable to get VPN to work, you can try accessng CRC through AVD. The AVD is a 
cloud-based Windows instance with direct access to PittNet.

A schematic of the process is depicted below.

![GETTING-STARTED-MAP](../_assets/img/avd/schematic-AVD.png)

^^**Definitions**^^

*   **Client** -- this is your computer or internet-connected device
*   **Access Portal** -- one of several remote servers used to submit jobs to the high performance computing clusters or to perform
data management operations
*   **CRC Ecosystem** -- the total footprint of the CRC infrastructure, including high performance computing 
clusters, data storage systems, networking equipment, and software 
*   **Azure Virtual Desktop** -- A cloud-based Windows Remote Desktop that has a direct connection to PittNet

^^**1. Install and Configure Remote Desktop client**^^

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

^^**2. Connecting to an AVD Device**^^

!!! example "Connecting to a Remote Device"
    === "MacOS"

        After successful authentication, you will be presented with list of authorized remote Devices that you can connect to. Your
        device list may be different from what is shown below. The *Device* we wish to log in to is **Pitt IT - Virtual Student Computing Lab**.

        ![MacOS-AVD-07](../_assets/img/avd/MacOS-AVD-07.png)

        Double clicking on that selection will prompt for your Pitt credentials.

        ![MacOS-AVD-08](../_assets/img/avd/MacOS-AVD-08.png)

        You will see the Remote Windows Desktop after successful login. From this remote portal, you can access CRC.

        ![MacOS-AVD-09](../_assets/img/avd/MacOS-AVD-09.png)

    === "Windows"

        After successful authentication, you will be presented with list of authorized Workspaces that you can connect to. Your
        Workspaces may be different from what is shown below. The *Workspace* we wish to log in to is **Pitt IT - Virtual Student Computing Lab**.

        ![Win-AVD-09](../_assets/img/avd/Win-AVD-09.png)

        Double clicking on that selection will prompt for your Pitt credentials.

        | Authenticate via Pitt Passport| |
        | ----------- | ------------------------------------ |
        | **1**![Win-AVD-10](../_assets/img/avd/Win-AVD-10.png) | **2**![Win-AVD-11](../_assets/img/avd/Win-AVD-11.png) |

        You will see the Remote Windows Desktop after successful login. From this remote portal, you can access CRC.

        ![Win-AVD-12](../_assets/img/avd/Win-AVD-12.png)

^^**3. Various Methods Connecting to CRC**^^

!!! example "Options for Connecting to CRC"

    === "Full Desktop Overview of all Tools"

        Shown below is the Desktop of the Virtual Computing Lab, where there are active connections
        to CRC using

        * **PuTTY**
        * **WinSCP**
        * **MobaXterm**
        * **Viz, Open OnDemand portals via a web browser**

        ![MacOS-AVD-All-Tools](../_assets/img/avd/MacOS-AVD-All-Tools.png)

        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>

    === "PuTTY"
        Search for and launch the PuTTY app from the Windows Start Menu.

        ![MacOS-AVD-PuTTY-01](../_assets/img/avd/MacOS-AVD-PuTTY-01.png)

        Fill in the PuTTY Configuration using the following values:

        * **Host Name (or IP address):** h2p.crc.pitt.edu or htc.crc.pitt.edu
        * **Port:** 22
        * **Connection type:** SSH

        You might also want to Save the profile under a name for quick loading in the future.

        ![MacOS-AVD-PuTTY-02](../_assets/img/avd/MacOS-AVD-PuTTY-02.png)

        When you first connect, you may see the following PuTTY Security Alert message below. Select Accept.

        ![MacOS-AVD-PuTTY-03](../_assets/img/avd/MacOS-AVD-PuTTY-03.png)

        The login credentials are your  Pitt username (all lowercase) and password.

        ![MacOS-AVD-PuTTY-04](../_assets/img/avd/MacOS-AVD-PuTTY-04.png)

        A successful authentication will present you with a terminal to one of the CRC login nodes.

        ![MacOS-AVD-PuTTY-05](../_assets/img/avd/MacOS-AVD-PuTTY-05.png)

    === "WinSCP"
        Transfer of files from the AVD to CRC can be done using WinSCP. Go to the Windows Start Menu
        and search for the WinSCP app.

        ![MacOS-AVD-WinSCP-01](../_assets/img/avd/MacOS-AVD-WinSCP-01.png)

        Fill in the Login Session panel using the following values:

        * **File protocol:** SFTP
        * **Host name:** h2p.crc.pitt.edu or htc.crc.pitt.edu
        * **Port number:** 22
        * **User name:** your Pitt username all in lowercase
        * **Password:** your Pitt password

        ![MacOS-AVD-WinSCP-02](../_assets/img/avd/MacOS-AVD-WinSCP-02.png)

        You might want to Save the session under a Site name for quick loading in the future.
        Press Login to initiate the connection.

        ![MacOS-AVD-WinSCP-03](../_assets/img/avd/MacOS-AVD-WinSCP-03.png)

        When you first connect, you may see the following Warning message below. Select Accept.

        ![MacOS-AVD-WinSCP-04](../_assets/img/avd/MacOS-AVD-WinSCP-04.png)

        Enter the password again if prompted.

        ![MacOS-AVD-WinSCP-05](../_assets/img/avd/MacOS-AVD-WinSCP-05.png)

        A successful connection will present a window with two panes. The left side shows 
        the filesystem on the AVD device, while the right side shows your $HOME folder 
        on CRC.

        ![MacOS-AVD-WinSCP-06](../_assets/img/avd/MacOS-AVD-WinSCP-06.png)

        You can transfer files between the two filesystems by dragging and dropping between the
        two panes.

        ![MacOS-AVD-WinSCP-07](../_assets/img/avd/MacOS-AVD-WinSCP-07.png)

        When terminating a session, you may be prompted with the option to save the workspace.

        ![MacOS-AVD-WinSCP-09](../_assets/img/avd/MacOS-AVD-WinSCP-09.png)

    === "MobaXterm"
        Within the AVD, point your browser to the [Download page](https://mobaxterm.mobatek.net/download-home-edition.html) 
        to get the Portable edition of the software.

        ![MacOS-AVD-MobaXterm-01](../_assets/img/avd/MacOS-AVD-MobaXterm-01.png)

        Navigate to the directory where the MobaXterm zip file was saved and double click on it to extract. Go into the 
        resulting software directory and run the MobaXterm program.

        ![MacOS-AVD-MobaXterm-02](../_assets/img/avd/MacOS-AVD-MobaXterm-02.png)

        Click on the Session widget to open the settings panel. Select the SSH option and fill in the Basic SSH setting 
        with the following values:

        * **Remote host:** h2p.crc.pitt.edu or htc.crc.pitt.edu
        * **Specify username:** your Pitt username in all lowercase
        * **Port:** 22

        followed by clicking on OK.

        ![MacOS-AVD-MobaXterm-03](../_assets/img/avd/MacOS-AVD-MobaXterm-03.png)

        Input your Pitt password. If the authentication becomes successful, you may be prompted to
        save your password. Make sure your Master Password is super strong if you decide to 
        save your Pitt password locally on the AVD device. Selecting No will result in MobaXterm
        prompting for your password when you login again. 

        ![MacOS-AVD-MobaXterm-05](../_assets/img/avd/MacOS-AVD-MobaXterm-05.png)

        A successful authentication will drop you onto the commandline on one of the CRC login nodes.
 
        ![MacOS-AVD-MobaXterm-06](../_assets/img/avd/MacOS-AVD-MobaXterm-06.png)

        By default, X-forwarding is enable on MobaXterm, giving you the capability to
        to display GUI applications through the included X server.

        ![MacOS-AVD-MobaXterm-07](../_assets/img/avd/MacOS-AVD-MobaXterm-07.png)

    === "CRC Webportals"

        From within the AVD, all CRC webportals are accessible, including

        * **Open OnDemand:** [https://ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu)
  
        ![MacOS-AVD-Webportals-01](../_assets/img/avd/MacOS-AVD-Webportals-01.png)

        ![MacOS-AVD-Webportals-02](../_assets/img/avd/MacOS-AVD-Webportals-02.png)

        * **Viz Linux Desktop:** [https://viz.crc.pitt.edu](https://viz.crc.pitt.edu)

        ![MacOS-AVD-Webportals-03](../_assets/img/avd/MacOS-AVD-Webportals-03.png)

        ![MacOS-AVD-Webportals-04](../_assets/img/avd/MacOS-AVD-Webportals-04.png)

        ![MacOS-AVD-Webportals-05](../_assets/img/avd/MacOS-AVD-Webportals-05.png)
