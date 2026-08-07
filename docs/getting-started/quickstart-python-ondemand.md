# Quick Start: Running Python in Open OnDemand

This page takes a brand-new user from a fresh account to running Python in a
Jupyter notebook on a compute node — all in the browser, with no SSH or Slurm
scripts. It is deliberately short and opinionated. Once a step makes sense,
follow the linked reference pages for the full detail.

!!! note "Before you begin"
    You need two things first:

    1. A **CRCD account and Resource Allocation** — see [Step 1: Getting an Account](getting-started-step1-account.md).
    2. An active **PittNet VPN (GlobalProtect)** connection if you're off campus — the portal is reachable only from within PittNet.

    Your username is your **Pitt username in all lowercase**. The full portal walkthrough,
    including logging in and managing files, is on [Open OnDemand](../web-portals/open-ondemand.md).

---

## 1. Open the Jupyter app

Sign in to [https://ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu) with your
Pitt credentials, then launch **Jupyter** from its tile on the Dashboard, or from
**Interactive Apps → Jupyter**. It runs either the classic Jupyter Notebook or JupyterLab on a
compute node.

!!! note "Not the same as JupyterHub on the Teach cluster"
    This Jupyter app runs inside Open OnDemand and is distinct from the standalone JupyterHub
    portal at `jupyter.crc.pitt.edu` that is used for teaching.

---

## 2. Request resources and launch

On the launch form, set the job parameters and click **Launch**. The fields that matter for a
first run:

- **Cluster** — the cluster to run on, e.g. `htc`.
- **Use JupyterLab instead of Jupyter Notebook?** — check for the JupyterLab IDE; leave
  unchecked for the classic Notebook.
- **Python version** — the Python module to load, e.g. `python/ondemand-jupyter-python3.11`.
- **Name of Custom Conda Environment** — leave blank to use the base environment (see Step 3).
- **Number of cores** — 1–128 (roughly 8 GB of memory per core); leave at `1` unless your code
  is genuinely parallel.
- **Number of hours** — the wall-time limit; the session ends when the time is up.
- **Account** — leave blank to use your default Resource Allocation.

Your session is queued while it waits for resources, then starts. When its card in **My
Interactive Sessions** shows **Running**, click **Connect to Jupyter** to open the interface in
a new tab.

!!! warning "Right-size your request"
    An interactive session holds a compute node and charges your allocation for the whole
    wall-time, used or not. Requesting more cores also means a longer wait in the queue — a few
    cores is plenty unless your code is parallel or memory-hungry. Remember to delete the session 
    when you're done (Step 5).

!!! tip "Working with GPUs?"
    For AI/ML, launch **Jupyter on gpu** instead (**Interactive Apps → Jupyter on gpu**, under the
    **Deep Learning** group). Its form bundles the framework, Python, and CUDA together — e.g.
    `pytorch 2.5.1 python 3.11 cuda 12.4` — and lets you request GPU cards. See
    [OnDemand Interactive Apps](../web-portals/open-ondemand-apps.md#jupyter-on-gpu).

---

## 3. Choose your Python

=== "Base environment"

    Leave **Name of Custom Conda Environment** blank on the launch form. The base Anaconda
    already includes common scientific packages (NumPy, pandas, matplotlib, …). If that covers
    your needs, there's nothing else to set up.

=== "Your own Conda environment"

    To use your own packages, create a Conda environment that **includes JupyterLab**, then point
    the form at it. From a terminal — **Clusters → HTC Shell Access** in OnDemand — run:

    ```bash
    module load python/ondemand-jupyter-python3.11   # run `module spider python` for the current version
    conda create -n myenv python=3.11 jupyterlab <your-packages>
    ```

    Then, on the Jupyter launch form, enter `myenv` (or the environment's full path) in
    **Name of Custom Conda Environment** before clicking **Launch**.

!!! tip "Keep large environments off your home directory"
    `/ihome` has a 75 GB quota and Conda environments grow quickly. For sizable environments,
    create them in your group storage with a full path — e.g.
    `conda create -p /ix/<group>/<username>/envs/myenv python=3.11 jupyterlab` — and enter that
    same path in **Name of Custom Conda Environment**. See
    [File Systems](../data-management/file-systems.md).

---

## 4. Run your code

In the Jupyter dashboard (or the JupyterLab launcher), open a new notebook and run a cell to
confirm you're on a compute node with the Python you expect:

```python
import sys, socket
print(sys.version)
print("running on", socket.gethostname())
```

The hostname should be a compute node (e.g. `htc-n…`), not a login node — your code is running
on the resources you requested.

!!! note "Your session is a Slurm job"
    The notebook runs on a dedicated compute node with exactly the resources you asked for — a
    normal Slurm job, not something in your browser. From a terminal inside JupyterLab,
    `squeue -M all -u $USER` lists it with its job ID, node, and partition. That's also why it
    waits in the queue, counts against your allocation, and must be ended with **Delete**.

---

## 5. End your session

Interactive sessions keep consuming your allocation until they stop. When you're finished, return
to **My Interactive Sessions** and click the red **Delete** button on the Jupyter card.
Otherwise the session ends on its own when the wall-time runs out.

---

## You're up and running

You have launched a Jupyter session, chosen a Python environment, and run code on a compute node.
Sensible next stops:

- [Quick Start: Your First Job](quickstart.md) — logging in, loading modules, and submitting batch jobs for unattended work
- [Running R in Open OnDemand](quickstart-r-ondemand.md) — the same workflow, for R in RStudio
- [OnDemand Interactive Apps](../web-portals/open-ondemand-apps.md#jupyter-notebook-and-lab) — every field on the Jupyter form, plus Jupyter on GPU, RStudio, and MATLAB
- [Open OnDemand](../web-portals/open-ondemand.md) — the full portal guide
- [Discovering Software](step3/getting-started-step3-software.md) — find and load modules
- [File Systems](../data-management/file-systems.md) — where to keep data and environments
- [Frequently Asked Questions](../faq.md) — quick answers to common snags

!!! question "Stuck?"
    Open a help ticket using the link on our
    [service catalog](https://crc.pitt.edu/service-request-forms).
