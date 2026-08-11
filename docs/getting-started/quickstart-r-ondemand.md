# Quick Start: Running R in Open OnDemand

This page takes a brand-new user from a fresh account to running R in the RStudio
IDE on a compute node — all in the browser, with no SSH or Slurm scripts. It is
deliberately short and opinionated. Once a step makes sense, follow the linked
reference pages for the full detail.

!!! note "Before you begin"
    You need two things first:

    1. A **CRCD account and Resource Allocation** — see [Step 1: Getting an Account](getting-started-step1-account.md).
    2. An active **PittNet VPN (GlobalProtect)** connection if you're off campus — the portal is reachable only from within PittNet.

    Your username is your **Pitt username in all lowercase**. The full portal walkthrough,
    including logging in and managing files, is on [Open OnDemand](../web-portals/open-ondemand.md).

---

## 1. Open the RStudio app

Sign in to [https://ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu) with your
Pitt credentials, then launch **RStudio Server** from the **RStudio Server 2026** tile on the
Dashboard, or from **Interactive Apps → RStudio Server**. It runs the RStudio IDE on a compute
node, so you can do compute-intensive R work.

---

## 2. Request resources and launch

On the launch form, set the job parameters and click **Launch**. The fields that matter for a
first run:

- **R version** — the R module to load, e.g. `4.6.0`.
- **Number of cores** — 1–128 (roughly 8 GB of memory per core); leave at `1` unless your code
  is genuinely parallel.
- **Number of hours** — the wall-time limit; the session ends when the time is up.
- **Memory (GB)** — an optional explicit memory request.
- **Account** — leave blank to use your default Resource Allocation.

Your session is queued while it waits for resources, then starts. When its card in **My
Interactive Sessions** shows **Running**, click **Connect to RStudio Server** to open the IDE in
a new tab.

!!! warning "Right-size your request"
    An interactive session holds a compute node and charges your allocation for the whole
    wall-time, used or not. Requesting more cores also means a longer wait in the queue — a few
    cores is plenty unless your code is parallel or memory-hungry. Remember to delete the session
    when you're done (Step 5).

!!! tip "Working with GPUs?"
    For GPU-accelerated R, launch **RStudio Server on gpu** instead (**Interactive Apps → RStudio
    Server on gpu**, under the **Deep Learning** group). Its form adds a **GPU type** (e.g.
    `l40s`), a node **constraint** (e.g. `amd,40g`), and **Number of gpu cards** (1–4). See
    [OnDemand Interactive Apps](../web-portals/open-ondemand-apps.md#rstudio-server-on-gpu).

---

## 3. Get your R packages

=== "Bundled packages"

    The R modules already bundle many CRAN and Bioconductor packages, so a lot of common
    libraries load with `library(...)` right away — nothing to set up.

=== "Install your own"

    Open a terminal — **Clusters → HTC Shell Access** in OnDemand — load the same R module that you
    specified in the form (use `module spider r` to show available versions), start `R`, and install what you need:

    ```r
    install.packages("pkg")                     # from CRAN
    BiocManager::install("Bioconductor_pkg")    # from Bioconductor
    ```

    `.libPaths()` shows where R searches for and installs packages; your personal library lives
    under your home directory.

---

## 4. Run your code

In RStudio, use the **Console** or open a new **R Script** and run a few lines to confirm you're
on a compute node with the R you expect:

```r
sessionInfo()                 # R version and loaded packages
Sys.info()[["nodename"]]      # the machine you're running on
```

The node name should be a compute node (e.g. `htc-n…`), not a login node — your code is running
on the resources you requested.

!!! note "Your session is a Slurm job"
    RStudio runs on a dedicated compute node with exactly the resources you asked for — a normal
    Slurm job, not something in your browser. From RStudio's **Terminal** tab,
    `squeue -M all -u $USER` lists it with its job ID, node, and partition. That's also why it
    waits in the queue, counts against your allocation, and must be ended with **Delete**.

!!! tip "If RStudio misbehaves"
    The interface is single-threaded, so a long-running command can make another action (like
    saving) time out with **Status code 502** — usually nothing crashed; wait for the command to
    finish and retry. **Status code 503** means you've hit your time limit; start a new session.
    RStudio also stores its state under `~/.local/share/rstudio` (75 GB home quota); if it fills
    up or you switch R versions, remove that folder (`rm -rf ~/.local/share/rstudio`) and it
    regenerates.

---

## 5. End your session

!!! warning "Quitting RStudio does not free the node"
    Choosing **File → Quit Session** (or the red icon in RStudio) only closes RStudio — it does
    **not** end your interactive session, and you keep consuming your allocation. To release the
    node, return to **My Interactive Sessions** and click the red **Delete** button.

---

## You're up and running

You have launched an RStudio session, set up your R packages, and run code on a compute node.
Sensible next stops:

- [Quick Start: Your First Job](quickstart.md) — logging in, loading modules, and submitting batch jobs for unattended work
- [Running Python in Open OnDemand](quickstart-python-ondemand.md) — the same workflow, for Python in Jupyter
- [OnDemand Interactive Apps](../web-portals/open-ondemand-apps.md#rstudio) — every field on the RStudio form, plus RStudio on GPU, Jupyter, and MATLAB
- [Open OnDemand](../web-portals/open-ondemand.md) — the full portal guide
- [Discovering Software](step3/getting-started-step3-software.md) — find and load modules
- [File Systems](../data-management/file-systems.md) — where to keep data and results
- [Frequently Asked Questions](../faq.md) — quick answers to common snags

!!! question "Stuck?"
    Open a help ticket using the link on our
    [service catalog](https://crc.pitt.edu/service-request-forms).
