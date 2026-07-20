# Transferring Files with Globus

[Globus](https://www.globus.org/) is a research data-transfer service that moves files
reliably and quickly between **collections** (endpoints) — for example, between CRCD storage
and another institution, a colleague's system, or your own computer. Transfers run in the
background, restart automatically after interruptions, and can be scheduled, which makes Globus
the best choice for large datasets, cross-institution transfers, and unattended moves.

CRCD's storage is exposed as the Globus collection **`pitt#dtn`** (the CRCD Data Transfer
Node), which reaches your `/ihome`, `/ix`, `/ix1`, and `/vast` paths.

!!! tip "No VPN required"
    Globus transfers go through the CRCD Data Transfer Node, so — unlike SSH or FileZilla — you
    do **not** need the PittNet VPN to move data with Globus.

## Log in to Globus

Go to [globus.org](https://www.globus.org/) and click **Log In**.

![The globus.org homepage with the Log In button](../../_assets/img/data-management/globus-1.png)

Under **Use your organizational login**, select **University of Pittsburgh** and click
**Continue**.

![Globus organizational login with University of Pittsburgh selected](../../_assets/img/data-management/globus-2.png)

You're redirected to **Pitt Passport**. Sign in with your Pitt username (all lowercase) and
password.

![Pitt Passport login page with username and password fields](../../_assets/img/data-management/globus-3.png)

Approve the **Duo** push notification, then confirm whether to remember the device.

![Duo Push prompt waiting for approval](../../_assets/img/data-management/globus-4.png)

![Duo 'Is this your device?' prompt](../../_assets/img/data-management/globus-5.png)

The next few screens appear only the **first time** you log in to Globus with your Pitt
identity. Click **Continue** at the welcome screen (or link an existing Globus account if you
have one)…

![Globus first-time welcome page offering to continue or link an existing account](../../_assets/img/data-management/globus-6.png)

…complete the sign-up (choose **non-profit research or educational purposes** and accept the
terms), and click **Continue**…

![Globus 'Complete Your Sign Up' page for a Pitt identity](../../_assets/img/data-management/globus-7.png)

…and **Allow** the Globus Web App the access it needs to manage your transfers.

![Globus consent screen granting the web app permission to manage transfers, groups, and identities](../../_assets/img/data-management/globus-8.png)

You land on the Globus **Dashboard**.

![The Globus Dashboard](../../_assets/img/data-management/globus-9.png)

## Connect to the CRCD collection

Open the **File Manager** from the left sidebar. It shows two panels — a source and a
destination — each of which needs a collection.

![The Globus File Manager two-pane view before choosing a collection](../../_assets/img/data-management/globus-10.png)

Click in a **Collection** search box, type `pitt#dtn`, and select the **pitt#dtn** collection
("DTN - CRC PITT").

![Collection search results showing the pitt#dtn collection](../../_assets/img/data-management/globus-11.png)

The first time you use a CRCD collection, Globus needs your consent to access it. Click
**Continue** on the "Authentication/Consent is required… Missing required data_access consent"
message…

![File Manager showing that data_access consent is required, with a Continue button](../../_assets/img/data-management/globus-12.png)

…authenticate with your **University of Pittsburgh** identity…

![Globus prompt to authenticate with the University of Pittsburgh identity](../../_assets/img/data-management/globus-13.png)

…and **Allow** Globus Transfer to manage data on the collection.

![Globus consent to manage data using Globus Transfer](../../_assets/img/data-management/globus-14.png)

The panel now lists your CRCD files at your path (for example, `/ihome/<group>/<username>`).
Set the **Path** to the folder you want, or point it at `/ix`, `/ix1`, or `/vast` for project
storage.

![File Manager listing CRCD files on pitt#dtn with the actions menu open](../../_assets/img/data-management/globus-15.png)

## Transfer files

With one panel on `pitt#dtn` and the other panel on your destination collection, select the
files or folders to move and click **Start** on the side you're transferring *from* — the arrow
on the **Start** button shows the direction. Use **Transfer & Timer Options** to enable sync
(only transfer new or changed files) or to schedule a recurring transfer. Globus emails you when
the transfer finishes, and you can watch progress under **Activity**.

## Move data to and from your own computer

To transfer to or from your laptop or desktop, install **Globus Connect Personal**, which turns
your computer into a Globus collection. In the collection search, choose **Get Globus Connect
Personal**.

![File Manager collection search with the Get Globus Connect Personal option](../../_assets/img/data-management/globus-16.png)

Download the version for your operating system.

![The Download Globus Connect Personal page](../../_assets/img/data-management/globus-17.png)

Install it — on macOS, drag the Globus icon into the **Applications** folder.

![Globus Connect Personal installer, dragging the icon to the Applications folder](../../_assets/img/data-management/globus-18.png)

![Globus Connect Personal shown in the macOS Applications folder](../../_assets/img/data-management/globus-19.png){ width="480" }

Launch it. The first time, confirm that you want to open an app downloaded from the internet.

![macOS confirming you want to open Globus Connect Personal](../../_assets/img/data-management/globus-20.png){ width="360" }

Globus Connect Personal then opens its setup. Click **Log In** and sign in with your Globus
(Pitt) account.

![Globus Connect Personal setup with a Log In button](../../_assets/img/data-management/globus-21.png){ width="480" }

**Allow** the setup the access it requests; you can give this computer a label for future
reference.

![Globus consent for Globus Connect Personal Setup, with a device-label field](../../_assets/img/data-management/globus-22.png)

Give the collection a **Collection Name** and description. Leave **High Assurance** unchecked
unless your institution subscribes to Globus at the High Assurance tier and your computer stores
sensitive data (PHI or Controlled Unclassified Information). Click **Save**.

![Globus Connect Personal Collection Details form with name and description](../../_assets/img/data-management/globus-23.png){ width="480" }

Setup is complete. Globus Connect Personal now runs in your menu bar, and your computer is
available as a collection (here named **Globus_laptop**).

![Globus Connect Personal 'Setup Successful' screen](../../_assets/img/data-management/globus-24.png){ width="480" }

## Transfer between CRCD and your computer

Back in the Globus **File Manager**, put `pitt#dtn` in one panel and search the other panel for
your new personal collection.

![File Manager with pitt#dtn on the left and an empty panel prompting to search for a collection](../../_assets/img/data-management/globus-25.png)

Type your collection name (or your username) and select your **Globus Connect Personal**
collection.

![Collection search finding the personal Globus_laptop collection](../../_assets/img/data-management/globus-26.png)

With both collections loaded, navigate each panel to the folder you want — your CRCD path on one
side and a folder on your computer on the other.

![File Manager showing pitt#dtn and the personal laptop collection side by side](../../_assets/img/data-management/globus-27.png)

Select the files or folders to move, then click **Start** on the source side. Here, files under
`/ihome/kwong/gnowmik/Downloads` transfer to `~/Desktop/Globus_test` on the laptop.

![File Manager with source and destination folders chosen for a transfer](../../_assets/img/data-management/globus-28.png)

Globus runs the transfer in the background and emails you when it finishes; the files then appear
in the destination collection.

![File Manager showing the transferred file present in both collections](../../_assets/img/data-management/globus-29.png)

## Log out and reconnect later

When you're finished, use **Logout** in the sidebar to sign out of the web app.

![Globus 'Logged out of Globus Web App' confirmation page](../../_assets/img/data-management/globus-30.png)

Globus Connect Personal keeps running in your menu bar, so your computer stays available for
transfers. To return to the web app later, click the **Globus Connect Personal** icon in the
menu bar and choose **Web: Transfer Files** — or choose **Quit Globus Connect Personal** to take
your computer offline.

![The Globus Connect Personal menu-bar menu, with Web: Transfer Files selected](../../_assets/img/data-management/globus-31.png)

Logging back in is the same as before — select **University of Pittsburgh** and authenticate.

![Globus organizational login with University of Pittsburgh selected](../../_assets/img/data-management/globus-32.png)

You return to the **File Manager**…

![The Globus File Manager two-pane view after logging back in](../../_assets/img/data-management/globus-33.png)

…and your CRCD and personal collections are waiting under **Recent** in the collection search, so
you can pick them again without searching.

![Collection search Recent tab listing pitt#dtn and the personal Globus_laptop collection](../../_assets/img/data-management/globus-34.png)

!!! tip "Where to put your data"
    For large datasets, transfer into your group's project storage (`/ix`, `/ix1`, `/vast`)
    rather than your 75 GB home directory — see
    [Storage Tiers](../../hardware_profiles/storage.md) and [File Systems](../file-systems.md).
