# Viz

## Summary

Viz is a set of nodes with a graphical desktop environment enabled. This page covers what you need to know to use it.

**[Log in to Viz](https://viz.crc.pitt.edu)**

You can treat this machine as another login node, with the added ability to run a graphical desktop. You can also submit
jobs to H2P or HTC from within the Viz interface.

## Accessing Viz

Like the other CRCD resources, Viz requires a connection to the
[GlobalProtect VPN](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=3426).

After connecting to the VPN, you can reach Viz either through the **web portal** or through the **FastX desktop
client**. The desktop client is worth using because copy/paste works significantly better than in the browser.

## Desktop client method

[Download the latest FastX client here](https://www.starnet.com/download/fastx-client). Install it somewhere you can
access, then run the executable from the installation directory.

When you run it, the window that opens is your **Connections** window:

![FastX Connections window](../_assets/img/web-portals/VizFastXDesktop_1.png)

Near the top-left corner, use the **plus** icon to add an **https** connection:

![Add an HTTPS connection](../_assets/img/web-portals/VizFastXDesktop_2.png)

Enter the following, then click **OK**:

| Setting | Value |
| ------- | ----- |
| Host | `viz.crc.pitt.edu` |
| User | Your Pitt ID, **all lowercase** |
| Port | `443` |
| Auth | `ssh` |
| Name | `Viz` |

![New connection details](../_assets/img/web-portals/VizFastXDesktop_3.png)

Double-click the new connection in the Connections window; you'll be prompted for your Pitt password:

![Password prompt](../_assets/img/web-portals/VizFastXDesktop_4.png)

The window that opens is your **Sessions** window, which works much like the web portal interface:

![Sessions window](../_assets/img/web-portals/VizFastXDesktop_5.png)

Use the **plus** icon to create a new session, select a desktop environment (MATE is a good default), and click **OK**:

![Create a new session](../_assets/img/web-portals/VizFastXDesktop_6.png)

Your session starts, and you can use this machine much like the H2P and HTC login nodes.

## Web portal method

Open [viz.crc.pitt.edu](https://viz.crc.pitt.edu/) in a web browser and log in with your Pitt username and password.
Your username is your Pitt ID, **all lowercase**. The screenshots below walk through accessing and using Viz in the
browser:

![Viz web portal — step 1](../_assets/img/web-portals/viz1.jpeg)

![Viz web portal — step 2](../_assets/img/web-portals/viz2.jpeg)

![Viz web portal — step 3](../_assets/img/web-portals/viz3.jpeg)

![Viz web portal — step 4](../_assets/img/web-portals/viz4.jpeg)

![Viz web portal — step 5](../_assets/img/web-portals/viz5.jpeg)

![Viz web portal — step 6](../_assets/img/web-portals/viz6.jpeg)

![Viz web portal — step 7](../_assets/img/web-portals/viz7.jpeg)

![Viz web portal — step 8](../_assets/img/web-portals/viz8.jpeg)

![Viz web portal — step 9](../_assets/img/web-portals/viz9.jpeg)

![Viz web portal — step 10](../_assets/img/web-portals/viz11.jpeg)

![Viz web portal — step 11](../_assets/img/web-portals/viz12.jpeg)

![Viz web portal — step 12](../_assets/img/web-portals/viz13.jpeg)

![Viz web portal — step 13](../_assets/img/web-portals/viz14.jpeg)

## FAQ

**When I log in via the web portal, I'm prompted for a second password. What do I enter?**

This happens when you type your username (Pitt ID) in uppercase instead of lowercase. Reconnect using all-lowercase
letters in your username.
