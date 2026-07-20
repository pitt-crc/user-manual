# Transferring Files with FileZilla

[FileZilla](https://filezilla-project.org/) is a free, cross-platform SFTP client with a
drag-and-drop interface for moving files between your computer and CRCD storage. It's a good
choice if you prefer a graphical tool over command-line `scp` or `rsync`. The screenshots below
are from macOS; the Windows and Linux clients work the same way.

!!! note "Connect to the VPN first"
    CRCD is reachable only from the University network. From off campus, connect to the
    [GlobalProtect VPN](../../getting-started/getting-started-step1-account.md) before connecting
    with FileZilla.

## Install and launch

Download the **FileZilla Client** from the [FileZilla download page](https://filezilla-project.org/download.php?show_all=1) and
install it. The first time you open it on macOS, confirm that you want to open an app downloaded
from the internet.

![macOS confirming you want to open FileZilla, downloaded from the internet](../../_assets/img/data-management/filezilla-1.png){ width="360" }

macOS may also ask FileZilla for permission to read a local location — such as a network volume —
so it can list files to transfer. Click **Allow**.

![macOS prompt to allow FileZilla to access files on a network volume](../../_assets/img/data-management/filezilla-2.png){ width="360" }

## Connect to CRCD

Fill in the **Quickconnect** bar at the top of the window:

| Field | Value |
| ----- | ----- |
| Host | `sftp://h2p.crc.pitt.edu` for SMP, MPI, or GPU; `sftp://htc.crc.pitt.edu` for HTC. The `sftp://` prefix forces a secure connection. |
| Username | Your Pitt username, all lowercase. |
| Password | Your Pitt password. |
| Port | `22` |

Then click **Quickconnect**.

![FileZilla Quickconnect bar filled in with host, username, password, and port 22](../../_assets/img/data-management/filezilla-3.png)

FileZilla asks whether to remember passwords. On your own computer, **Save passwords** is
convenient; on a shared machine, choose **Do not save passwords** or protect them with a master
password.

![FileZilla 'Remember passwords?' dialog](../../_assets/img/data-management/filezilla-4.png)

The first time you connect to a host, FileZilla shows the server's host key. Check **Always trust
this host, add this key to the cache** and click **OK**.

![FileZilla 'Unknown host key' dialog showing the server fingerprint](../../_assets/img/data-management/filezilla-5.png)

Once connected, the **Remote site** panel on the right shows your CRCD home directory
(`/ihome/<group>/<username>`); the **Local site** panel on the left is your own computer.

![FileZilla connected, with the remote site showing the /ihome home directory](../../_assets/img/data-management/filezilla-6.png)

## Allow access to local folders

As you browse your computer to pick files, macOS asks FileZilla for permission to each personal
folder. Click **Allow** on each so FileZilla can upload from and download to them.

=== "OneDrive"

    ![macOS prompt to allow FileZilla to access files managed by OneDrive](../../_assets/img/data-management/filezilla-7a.png){ width="360" }

=== "Desktop"

    ![macOS prompt to allow FileZilla to access your Desktop folder](../../_assets/img/data-management/filezilla-7b.png){ width="360" }

=== "Documents"

    ![macOS prompt to allow FileZilla to access your Documents folder](../../_assets/img/data-management/filezilla-7c.png){ width="360" }

=== "Downloads"

    ![macOS prompt to allow FileZilla to access your Downloads folder](../../_assets/img/data-management/filezilla-7d.png){ width="360" }

## Transfer files

With both panels showing the folders you want, move files by dragging them between **Local site**
and **Remote site**, or by double-clicking a file to send it to the other side. Progress appears
in the transfer queue at the bottom, and finished transfers move to the **Successful transfers**
tab.

![FileZilla after a successful transfer, with the file now in the remote Downloads folder](../../_assets/img/data-management/filezilla-8.png)

!!! tip "Where to put your data"
    Your home directory has a 75 GB quota. For large datasets, transfer into your group's project
    storage (`/ix`, `/ix1`, `/vast`) instead — see
    [Storage Tiers](../../hardware_profiles/storage.md) and [File Systems](../file-systems.md).

## Reconnect later

FileZilla remembers recent connections. Next time, click the arrow next to **Quickconnect** and
pick your saved host from the history instead of retyping it.

![FileZilla Quickconnect dropdown showing the saved sftp connection in history](../../_assets/img/data-management/filezilla-9.png)
