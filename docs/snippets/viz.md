# Linux Desktop Visualization (Viz)

The Viz nodes provide a full graphical Linux desktop, accessed through FastX. You can
connect in three ways: straight from your **web browser** with nothing to install, or with
the **FastX desktop client** on **macOS** or **Windows** — a small app that generally
handles copy and paste better and lets you save connection profiles you can reopen with a
double-click. Use it for GUI (Graphical User Interface) applications, visualization, and
previewing results, or as a desktop gateway to the clusters. Pick your method in the tabs
below. See the [Viz node hardware](https://crc-pages.pitt.edu/user-manual/hardware_profiles/viz/)
for specifications.

!!! note "Connect to the VPN (Virtual Private Network — required to reach the clusters) first"

    Like the other CRCD resources, Viz is reachable only from the University network. From
    off campus, connect to the
    [GlobalProtect VPN (Virtual Private Network — required to reach the clusters)](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=3426)
    first (see [Step 1](https://crc-pages.pitt.edu/user-manual/getting-started/getting-started-step1-account/)).

=== "Web browser"

    The browser client runs the desktop in a new tab with nothing to install — the quickest
    way to get a Viz session.

    **Step 1. Log in to FastX**

    Point your browser to <https://viz.crc.pitt.edu> and sign in with your Pitt credentials.
    Enter your Pitt username (1) in all lowercase and your Pitt password (2), then click
    **SSH (Secure Shell — protocol for connecting to a login node) Login** (3).

    ![FastX SSH login page with the Username field, Password field, and SSH Login button](../_assets/img/web-portals/fastx-1.png)

    !!! warning "Use all lowercase for your username"

        If you are unexpectedly prompted for a second password, you most likely entered your
        username with capital letters. Reload the page and sign in again using your Pitt
        username in all lowercase.

    **Step 2. Launch a desktop session**

    After logging in you land on the **My Sessions** page. The **Applications** panel on the
    left lists the available desktops and terminals — **mate**, **xfce**, and **xterm**. Click
    **mate** (a good default desktop) to start a new session.

    ![FastX My Sessions page showing the Applications list (mate, xfce, xterm) with mate selected](../_assets/img/web-portals/fastx-2.png)

    **Step 3. Choose how to connect**

    FastX asks how you want to open the session. Choose **Browser Client** to run the desktop
    in a new browser tab with nothing to install; the **Open in New Tab** option is selected
    so the desktop opens in its own tab. The **Desktop Client** is an alternative that
    generally handles copy and paste better — see the **macOS client** or **Windows client**
    tab above to set it up.

    ![FastX prompt asking how to connect to the session, with Browser Client and Desktop Client options](../_assets/img/web-portals/fastx-3.png)

    **Step 4. Use the desktop**

    Your MATE desktop opens in the browser. Use it like any Linux desktop: the
    **Applications**, **Places**, and **System** menus are in the top-left, and the desktop
    icons give you your home folder, Computer, and Trash.

    ![MATE desktop session running in the FastX browser client](../_assets/img/web-portals/fastx-5.png)

    If the desktop does not fill the window — as below, where the StarNet background shows
    around a smaller desktop — use the toolbar on the right edge of the session to fit or
    resize it.

    ![MATE desktop shown smaller than the browser window, with the StarNet watermark border visible](../_assets/img/web-portals/fastx-4.png)

    **Step 5. Run an application**

    Open a terminal from the desktop by clicking the terminal icon in the top menu bar (1),
    which starts **Mate Terminal** (2). From there you can load software with the module
    system and launch it. For example, to start MATLAB:

    ```
    module load matlab
    matlab
    ```

    ![MATE desktop with a terminal running 'module load matlab' and MATLAB R2025a open](../_assets/img/web-portals/fastx-6.png)

    **Step 6. Reconnect or end your session**

    Your session keeps running on the Viz node even if you close the browser tab, navigate
    away, or lose your network connection — you are only *disconnected*, not logged out. To
    pick up where you left off, return to <https://viz.crc.pitt.edu>: any running session
    appears under **Connected Sessions** on the My Sessions page. Click its thumbnail to
    reconnect exactly where you left it — handy for long-running GUI (Graphical User
    Interface) work you come back to over several sittings.

    ![FastX My Sessions page showing a running 'mate' session under Connected Sessions](../_assets/img/web-portals/fastx-16.png)

    !!! note "Reconnect or terminate from My Sessions"

        From the **My Sessions** page you can click a **Connected Session** to reconnect, or
        terminate it there without reopening it. Closing the browser tab only disconnects —
        the session (and its resource use) keeps running until you log out or terminate it.

    Because a disconnected session keeps consuming resources, **log out** from inside the
    desktop when you are truly finished. Click through the sequence below.

    **1. Save work** — save your work and close any open applications; here, LibreOffice,
    launched with `module load libreoffice` then `soffice`.

    ![LibreOffice and a terminal open on the MATE desktop before logging out](../_assets/img/web-portals/fastx-7.png)

    **2. Log out** — from the desktop menu bar, open **System** and choose **Log Out
    gnowmik…**. (The **Shut Down…** entry just below does the same thing — it ends your
    session, not the shared Viz node.)

    ![The MATE System menu open with 'Log Out gnowmik…' highlighted](../_assets/img/web-portals/fastx-8.png)

    **3. Confirm** — click **Log Out** to confirm, or just wait for the short countdown to
    log you out automatically.

    ![MATE 'Log out of this system now?' dialog with Switch User, Cancel, and Log Out buttons](../_assets/img/web-portals/fastx-9.png)

    **4. Disconnected** — FastX confirms the session exited normally and its resources are
    freed. Click **Home** to return to My Sessions.

    ![FastX 'Client Disconnected — the session exited normally' message with a Home button](../_assets/img/web-portals/fastx-10.png)

    Back on the My Sessions page, **No Sessions Running** confirms the session has fully ended
    and its resources are freed — compare with **Connected Sessions** above, where it was
    still available to reconnect.

    ![FastX My Sessions page showing No Sessions Running](../_assets/img/web-portals/fastx-11.png)

=== "macOS client"

    The desktop client is a small app you install on your Mac. It generally handles copy and
    paste better than the browser and lets you save connection profiles you can reopen with a
    double-click.

    **Step 1. Download and install FastX**

    Download the macOS FastX client from <https://www.starnet.com/download-fastx-client/> —
    click **MacOS Client** — and open the `.dmg` file. In the window that opens, drag the
    **FastX5** icon onto the **Applications** folder shortcut to install it.

    ![FastX5 disk image window with the FastX5 app being dragged onto the Applications folder](../_assets/img/desktop-clients/fastx_mac_desktop_01.png)

    The first time you launch FastX, macOS warns that it is an app downloaded from the
    Internet. Click **Open** to continue. (You only need to confirm this once.) Launch FastX
    from your **Applications** folder — not from the mounted disk image.

    ![macOS dialog warning that FastX5 was downloaded from the internet, with Cancel and Open buttons](../_assets/img/desktop-clients/fastx_mac_desktop_02.png){ width="400" }

    !!! tip "If macOS blocks the app"

        If macOS refuses to open the app instead of offering an **Open** button, right-click
        (or Control-click) **FastX5** in your Applications folder and choose **Open** from the
        menu, then confirm.

    **Step 2. Add a connection**

    When FastX opens you land on the **Connections** tab. Click the **+** button (1) to create
    a new connection to the Viz node, then fill in the **Edit Connection** dialog (2) with the
    settings below and click **OK**.

    | Field | Value |
    |---|---|
    | Protocol | **https** |
    | Host | `viz.crc.pitt.edu` |
    | User | your Pitt username, in **all lowercase** |
    | Port | `443` |
    | Uri | *leave blank* |
    | Auth | **SSH** (Secure Shell — protocol for connecting to a login node) |
    | Name | `viz` (or any label you like) |

    Under **Advanced**, check **Ignore SSL errors**. Leave **Route all Connections Through
    Gateway** and **Disable URL Open** unchecked.

    | Add the viz connection | |
    | --- | --- |
    | **1** ![FastX Connections tab with no connections yet and the plus button highlighted](../_assets/img/desktop-clients/fastx_mac_desktop_03.png) | **2** ![FastX Edit Connection dialog filled in with host viz.crc.pitt.edu, user, port 443, SSH auth, and Ignore SSL errors checked](../_assets/img/desktop-clients/fastx_mac_desktop_04.png) |

    !!! warning "Use all lowercase for your username"

        Enter your Pitt username in all lowercase. Capital letters can cause FastX to
        unexpectedly prompt you for a second password.

    **Step 3. Connect and log in**

    Your saved **viz** connection now appears on the Connections tab. Double-click it to
    connect (1). When prompted, enter your Pitt **username** in all lowercase and your Pitt
    **password**, then click **Continue** (2). (On the connection tile, the pencil icon edits
    the connection, the heart marks it as a favorite, and the **✕** removes it.)

    | Connect and log in | |
    | --- | --- |
    | **1** ![FastX Connections tab showing the saved viz connection selected, with edit, favorite, and remove buttons](../_assets/img/desktop-clients/fastx_mac_desktop_05.png) | **2** ![FastX login dialog with Username and Password fields and a Continue button](../_assets/img/desktop-clients/fastx_mac_desktop_06.png) |

    **Step 4. Launch a desktop session**

    After logging in you land on the **My Sessions** page for the viz connection. Click the
    **+** button to start a new session.

    ![FastX My Sessions page for the viz connection with the plus button highlighted](../_assets/img/desktop-clients/fastx_mac_desktop_07.png)

    The **Applications** picker lists the available desktops and terminals — **mate**,
    **xfce**, and **xterm**, plus VirtualGL variants for hardware-accelerated graphics. Select
    **mate** (a good default desktop) and click **OK**.

    ![FastX Global Applications picker showing mate, xfce, and xterm icons with mate selected](../_assets/img/desktop-clients/fastx_mac_desktop_08.png)

    !!! tip "VirtualGL variants"

        The **mate - VirtualGL** and **xfce - VirtualGL** entries run the desktop with
        [VirtualGL](https://crc-pages.pitt.edu/user-manual/bioinformatics/virtualgl/) for
        hardware-accelerated OpenGL. Choose one of these if your application needs 3D graphics.

    **Step 5. Use the desktop**

    Your MATE desktop opens in its own window. Use it like any Linux desktop: the
    **Applications**, **Places**, and **System** menus are in the top-left, and the desktop
    icons give you your home folder, Computer, and Trash.

    ![MATE desktop session running in the FastX desktop client on macOS](../_assets/img/desktop-clients/fastx_mac_desktop_09.png)

    If you step away, the session may lock itself after a period of inactivity. When it does,
    MATE shows a lock screen — type your Pitt password and click **Unlock** to resume.

    ![MATE screen-lock dialog asking for a password, with Leave Message, Cancel, and Unlock buttons](../_assets/img/desktop-clients/fastx_mac_desktop_10.png)

    **Step 6. Open a terminal and run software**

    To open a terminal, right-click anywhere on the desktop background and choose **Open in
    Terminal**.

    ![MATE desktop with the right-click menu open and 'Open in Terminal' highlighted](../_assets/img/desktop-clients/fastx_mac_desktop_11.png)

    A terminal opens. From there you can load software with the module system and launch it —
    for example:

    ```
    module load matlab
    matlab
    ```

    ![MATE terminal open at the gnowmik@viz-n3 prompt with MATLAB launching](../_assets/img/desktop-clients/fastx_mac_desktop_12.png)

    **Step 7. Reconnect or end your session**

    Your session keeps running on the Viz node even if you close the FastX window or lose your
    network connection — you are only *disconnected*, not logged out. To pick up where you
    left off, reopen FastX, connect to **viz** again, and click the running session's **▶** on
    the My Sessions page to reconnect exactly where you left it.

    ![FastX My Sessions page showing a running mate session tile with a play button to reconnect](../_assets/img/desktop-clients/fastx_mac_desktop_13.png)

    !!! note "Log out when you are truly finished"

        Because a disconnected session keeps consuming resources, **log out** from inside the
        desktop when you are done: from the desktop menu bar open **System** and choose
        **Log Out**, then confirm. Closing the FastX window only disconnects — the session
        (and its resource use) keeps running until you log out or terminate it.

=== "Windows client"

    The desktop client is a small app you install on your Windows PC. It generally handles
    copy and paste better than the browser and lets you save connection profiles you can
    reopen with a double-click.

    **Step 1. Download FastX**

    Open <https://www.starnet.com/download-fastx-client/> in your browser and click
    **Windows Client (exe)** to download the installer. (If you do not have administrator
    rights on your PC, use the **Windows Non-Admin Client** link instead.)

    ![StarNet Download FastX Client page with Windows, MacOS, and Linux download options](../_assets/img/desktop-clients/fastx_win_desktop_01.png)

    **Step 2. Run the installer**

    Double-click the downloaded `.exe` and work through the setup wizard, following the panels
    below in order:

    1. **User Account Control** asks whether to allow the app to make changes (publisher **StarNet Communications Corp.**) — click **Yes**.
    2. On the welcome screen, click **Next**.
    3. Read the license agreement, check **I accept the terms in the License Agreement**, and click **Next**.
    4. Leave the default install location (and the desktop-shortcut option, if you want one) and click **Next**.
    5. Click **Install** to begin.
    6. When the wizard finishes, click **Finish** to close it.

    | Install the FastX client | |
    | --- | --- |
    | **1** ![Windows User Account Control prompt for the FastX Installer with the Yes button highlighted](../_assets/img/desktop-clients/fastx_win_desktop_02.png) | **2** ![StarNet FastX 5 Setup welcome screen with a Next button](../_assets/img/desktop-clients/fastx_win_desktop_03.png) |
    | **3** ![StarNet FastX 5 End-User License Agreement with the accept checkbox and Next button](../_assets/img/desktop-clients/fastx_win_desktop_04.png) | **4** ![StarNet FastX 5 Destination Folder screen showing the default install path and a Next button](../_assets/img/desktop-clients/fastx_win_desktop_05.png) |
    | **5** ![StarNet FastX 5 'Ready to install' screen with an Install button](../_assets/img/desktop-clients/fastx_win_desktop_06.png) | **6** ![StarNet FastX 5 'Completed the Setup Wizard' screen with a Finish button](../_assets/img/desktop-clients/fastx_win_desktop_07.png) |

    **Step 3. Launch FastX and add a connection**

    Open the **Start** menu (1) and click **FastX 5** (2) to launch the client. (It also
    appears on the desktop if you kept the shortcut, or you can search for "FastX".)

    ![Windows Start menu with FastX 5 in the Recommended list](../_assets/img/desktop-clients/fastx_win_desktop_08.png){ width="450" }

    FastX opens on the **Connections** tab. Click the **+** button (1) to create a new
    connection to the Viz node, then fill in the **Edit Connection** dialog (2) with the
    settings below and click **OK**.

    | Field | Value |
    |---|---|
    | Protocol | **https** |
    | Host | `viz.crc.pitt.edu` |
    | User | your Pitt username, in **all lowercase** |
    | Port | `443` |
    | Uri | *leave blank* |
    | Auth | **SSH** (Secure Shell — protocol for connecting to a login node) |
    | Name | `viz` (or any label you like) |

    Under **Advanced**, check **Ignore SSL errors**. Leave **Route all Connections Through
    Gateway** and **Disable URL Open** unchecked.

    | Add the viz connection | |
    | --- | --- |
    | **1** ![FastX Connections tab with no connections yet and the plus button highlighted](../_assets/img/desktop-clients/fastx_win_desktop_09.png) | **2** ![FastX Edit Connection dialog filled in with host viz.crc.pitt.edu, user, port 443, SSH auth, and Ignore SSL errors checked](../_assets/img/desktop-clients/fastx_win_desktop_10.png) |

    !!! warning "Use all lowercase for your username"

        Enter your Pitt username in all lowercase. Capital letters can cause FastX to
        unexpectedly prompt you for a second password.

    **Step 4. Connect and log in**

    Your saved **viz** connection now appears on the Connections tab. Double-click it to
    connect (1). When prompted, enter your Pitt **username** in all lowercase and your Pitt
    **password**, then click **Continue** (2). (On the connection tile, the pencil icon edits
    the connection, the heart marks it as a favorite, and the **✕** removes it.)

    | Connect and log in | |
    | --- | --- |
    | **1** ![FastX Connections tab showing the saved viz connection selected, with edit, favorite, and remove buttons](../_assets/img/desktop-clients/fastx_win_desktop_11.png) | **2** ![FastX login dialog with Username and Password fields and a Continue button](../_assets/img/desktop-clients/fastx_win_desktop_12.png) |

    **Step 5. Launch or reconnect to a desktop session**

    After logging in you land on the **My Sessions** page. Click the **+** button to start a
    new session. Any session you already have appears here as a tile — click its **▶** (play)
    button to reconnect to it exactly where you left off.

    ![FastX My Sessions page with a mate session tile showing a play button, and the plus button to add a session](../_assets/img/desktop-clients/fastx_win_desktop_13.png)

    If you reconnect to a session that locked itself while you were away, MATE asks for your
    password. Type your Pitt password and click **Unlock**.

    ![MATE screen-lock dialog asking for a password, with Leave Message, Cancel, and Unlock buttons](../_assets/img/desktop-clients/fastx_win_desktop_14.png)

    Your MATE desktop opens in its own window. Use it like any Linux desktop: the
    **Applications**, **Places**, and **System** menus are in the top-left, and the desktop
    icons give you your home folder, Computer, and Trash.

    ![MATE desktop session running in the FastX desktop client on Windows](../_assets/img/desktop-clients/fastx_win_desktop_15.png)

    **Step 6. Launch another desktop and open a terminal**

    To start a different desktop, click **+** on the My Sessions page. The **Applications**
    picker lists the available desktops and terminals — **mate**, **xfce**, and **xterm**, plus
    VirtualGL variants for hardware-accelerated graphics. Select one — here **xfce** — and
    click **OK**.

    ![FastX Global Applications picker showing mate, xfce, and xterm icons with xfce selected and the OK button highlighted](../_assets/img/desktop-clients/fastx_win_desktop_16.png)

    !!! tip "VirtualGL variants"

        The **mate - VirtualGL** and **xfce - VirtualGL** entries run the desktop with
        [VirtualGL](https://crc-pages.pitt.edu/user-manual/bioinformatics/virtualgl/) for
        hardware-accelerated OpenGL. Choose one of these if your application needs 3D graphics.

    In the XFCE desktop, right-click anywhere on the background and choose **Open Terminal
    Here**.

    ![XFCE desktop with the right-click menu open and 'Open Terminal Here' highlighted](../_assets/img/desktop-clients/fastx_win_desktop_17.png)

    A terminal opens. From there you can load software with the module system and launch it —
    for example:

    ```
    module load matlab
    matlab
    ```

    ![XFCE desktop with a terminal open at the gnowmik@viz-n3 prompt](../_assets/img/desktop-clients/fastx_win_desktop_18.png)

    **Step 7. Reconnect or end your session**

    Your session keeps running on the Viz node even if you close the FastX window or lose your
    network connection — you are only *disconnected*, not logged out. To pick up where you
    left off, reopen FastX, connect to **viz** again, and click the running session's **▶** on
    the My Sessions page to reconnect exactly where you left it.

    !!! note "Log out when you are truly finished"

        Because a disconnected session keeps consuming resources, **log out** from inside the
        desktop when you are done: from the desktop menu bar open **System** and choose
        **Log Out**, then confirm. Closing the FastX window only disconnects — the session
        (and its resource use) keeps running until you log out or terminate it.

## Definitions

- **FastX** — the StarNet remote-desktop software that streams a Linux graphical desktop to your browser or to a local desktop client.
- **desktop client** — the FastX app installed on your Mac or PC; an alternative to the browser client that handles copy and paste better and stores saved connections.
- **MATE / XFCE** — lightweight Linux desktop environments; either works for a Viz session, and MATE is a good default.
- **VirtualGL** — a toolkit that gives a remote Linux session hardware-accelerated OpenGL for 3D graphics.
- **session** — a running desktop or application on a Viz node; sessions persist until you terminate them.
