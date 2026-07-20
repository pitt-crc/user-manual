# OnDemand Interactive Apps

This page covers the individual interactive applications available through
[Open OnDemand](open-ondemand.md) — Jupyter, Jupyter on GPU, RStudio, RStudio on GPU, and
MATLAB. For logging in, managing files, and the general launch/connect/delete pattern, see the
[Open OnDemand](open-ondemand.md) overview.

**Jump to an app:**

<div class="grid cards" id="apps" markdown>

-   :material-notebook:{ .lg .middle } __Jupyter__

    ---

    Jupyter Notebook or JupyterLab on a CPU node.

    [:octicons-arrow-right-24: Jupyter](#jupyter-notebook-and-lab)

-   :material-notebook:{ .lg .middle } __Jupyter on GPU__

    ---

    Jupyter Notebook or JupyterLab on a GPU node.

    [:octicons-arrow-right-24: Jupyter on GPU](#jupyter-on-gpu)

-   :material-chart-line:{ .lg .middle } __RStudio__

    ---

    RStudio Server on a CPU node.

    [:octicons-arrow-right-24: RStudio](#rstudio)

-   :material-chart-line:{ .lg .middle } __RStudio on GPU__

    ---

    RStudio Server on a GPU node.

    [:octicons-arrow-right-24: RStudio Server on GPU](#rstudio-server-on-gpu)

-   :material-calculator-variant:{ .lg .middle } __MATLAB__

    ---

    The MATLAB desktop, served through the browser by `matlab-proxy`.

    [:octicons-arrow-right-24: MATLAB](#matlab)

</div>

## Jupyter Notebook and Lab

[↑ Back to app menu](#apps)

The **Jupyter** app runs Jupyter Notebook or JupyterLab on a compute node with dedicated
resources. (This is the Jupyter app inside Open OnDemand — distinct from the standalone
JupyterHub portal at `jupyter.crc.pitt.edu`.) Launch it from the **Jupyter** tile on the
Dashboard, or from **Interactive Apps → Jupyter**.

![Open OnDemand Dashboard with the Jupyter pinned app highlighted](../_assets/img/web-portals/ondemand-jupyter-1.png)

On the launch form, set the job parameters and click **Launch**:

| Field | What it does |
| ----- | ------------ |
| Cluster | Which cluster the session runs on (for example, `htc`). |
| Use JupyterLab instead of Jupyter Notebook? | Check for the JupyterLab IDE; leave unchecked for the classic Notebook. |
| Python version | The Python module to load (for example, `python/ondemand-jupyter-python3.11`). |
| Name of Custom Conda Environment | Leave blank to use the base environment. Otherwise, give the name (or full path) of a conda environment that already has JupyterLab installed. |
| Number of hours | Wall-time limit for the session. |
| Number of cores | 1–128 cores, roughly 8 GB of memory per core unless you request a whole node. |
| Memory (GB) | Optional explicit memory request. |
| Account | The Slurm allocation to charge; leave blank to use your default. |

!!! tip "Don't over-request cores"
    Unless your code is genuinely parallel (for example, thread-parallelized NumPy) or
    memory-hungry, a few cores is plenty — larger requests wait longer in the queue.

![Top of the Jupyter launch form: cluster, JupyterLab option, and Python version](../_assets/img/web-portals/ondemand-jupyter-2a.png)

![Bottom of the Jupyter launch form: cores, memory, account, and the Launch button](../_assets/img/web-portals/ondemand-jupyter-2b.png)

Your session is queued while it waits for resources, then starts running. When the card shows
**Running**, click **Connect to Jupyter** to open the interface in a new tab.

![My Interactive Sessions showing the Jupyter session queued](../_assets/img/web-portals/ondemand-jupyter-3.png)

![My Interactive Sessions showing the Jupyter session running, with a Connect to Jupyter button](../_assets/img/web-portals/ondemand-jupyter-4.png)

![JupyterLab running in the browser with the file browser and a notebook open](../_assets/img/web-portals/ondemand-jupyter-5.png)

When you're done, return to **My Interactive Sessions** and click the red **Delete** button to
end the session and free the resources.

## Jupyter on GPU

[↑ Back to app menu](#apps)

Jupyter on GPU is the same app running on a GPU node. It has no pinned tile — open it from
**Interactive Apps → Jupyter on gpu** (under the **Deep Learning** group).

![Interactive Apps menu open, with Jupyter on gpu listed under the Deep Learning group](../_assets/img/web-portals/ondemand-jupyter-gpu-1.png)

On the launch form, set the job parameters and click **Launch**:

| Field | What it does |
| ----- | ------------ |
| Use JupyterLab instead of Jupyter Notebook? | Check for the JupyterLab IDE; leave unchecked for the classic Notebook. |
| Python version | Bundles the framework, Python, and CUDA together — for example, `pytorch 2.5.1 python 3.11 cuda 12.4`. |
| Cuda version | A separate CUDA module to load. Choose **None** if you're using PyTorch with its bundled CUDA packages. |
| Number of hours | Wall-time limit for the session. |
| Number of cores | 1–128 cores, roughly 8 GB of memory per core unless you request a whole node. |
| CPU Memory (GB) | Optional explicit memory request. |
| GPU type | Which GPU/node type to request (for example, `rtx6k,96g,amd(avx512)`). |
| Number of gpu cards | 1–4 cards on the node. |
| Account | The Slurm allocation to charge; leave blank to use your default. |
| Reservation | Leave blank unless you've been given a reservation. |

![Top of the Jupyter on gpu launch form: JupyterLab option, Python/framework version, and CUDA version](../_assets/img/web-portals/ondemand-jupyter-gpu-2a.png)

![Bottom of the Jupyter on gpu launch form: GPU type, number of GPU cards, account, and the Launch button](../_assets/img/web-portals/ondemand-jupyter-gpu-2b.png)

Click **Launch**. As before, the session is queued, then runs; when it shows **Running**, click
**Connect to Jupyter**.

![My Interactive Sessions showing the Jupyter on gpu session queued](../_assets/img/web-portals/ondemand-jupyter-gpu-3.png)

![My Interactive Sessions showing the Jupyter on gpu session running on a GPU node, with a Connect to Jupyter button](../_assets/img/web-portals/ondemand-jupyter-gpu-4.png)

![JupyterLab running in the browser on a GPU node](../_assets/img/web-portals/ondemand-jupyter-gpu-5.png)

!!! note "Your Jupyter session is a Slurm job"
    The session runs on a dedicated compute node with exactly the resources you requested on
    the form — it is a normal Slurm job, not something running in your browser or on a shared
    server. From a terminal inside JupyterLab, `squeue -M all -u $USER` lists it with its job
    ID, node, and partition. This is also why the session waits in the queue, counts against
    your allocation, and must be ended with **Delete**.

![Terminal inside JupyterLab showing the session listed as a Slurm job by squeue](../_assets/img/web-portals/ondemand-jupyter-gpu-6.png)

When you're done, end the session with the red **Delete** button, the same as any interactive app.

## RStudio

[↑ Back to app menu](#apps)

RStudio Server runs the RStudio IDE in the browser on a compute node, so you can run more
compute-intensive R work than on a login node. Launch it from the **RStudio Server 2026** tile
on the Dashboard, or from **Interactive Apps → RStudio Server**.

![Open OnDemand Dashboard with the RStudio Server 2026 pinned app](../_assets/img/web-portals/ondemand-rstudio-1.png)

On the launch form, set the job parameters and click **Launch**:

| Field | What it does |
| ----- | ------------ |
| R version | The R module to load (for example, `4.6.0`). |
| Number of hours | Wall-time limit for the session. |
| Number of cores | 1–128 cores, roughly 8 GB of memory per core unless you request a whole node. |
| Memory (GB) | Optional explicit memory request. |
| Account | The Slurm allocation to charge; leave blank to use your default. |
| Reservation | Leave blank unless you've been given a reservation. |

![Top of the RStudio Server launch form: R version, hours, and cores](../_assets/img/web-portals/ondemand-rstudio-2a.png)

![Bottom of the RStudio Server launch form: memory, account, reservation, and the Launch button](../_assets/img/web-portals/ondemand-rstudio-2b.png)

The session is queued, then runs; when it shows **Running**, click **Connect to RStudio Server**.

![My Interactive Sessions showing the RStudio Server session queued](../_assets/img/web-portals/ondemand-rstudio-3.png)

![My Interactive Sessions showing the RStudio Server session running, with a Connect to RStudio Server button](../_assets/img/web-portals/ondemand-rstudio-4.png)

![RStudio Server IDE running in the browser](../_assets/img/web-portals/ondemand-rstudio-5.png)

The R modules bundle many R and Bioconductor packages. To install your own, open **Clusters →
HTC Shell Access**, load an R module (find the current one with `module spider r`), start `R`,
and use `install.packages("pkg")` or `BiocManager::install("Bioconductor_pkg")`. Use
`.libPaths()` to see where R searches.

### Common RStudio errors

RStudio Server stores its state in your home directory (`~/.local/share/rstudio` or the legacy
`~/.rstudio`), which has a 75 GB quota. Large project files or timed-out sessions can fill it; you
can delete unused sessions from `~/.local/share/rstudio/sessions`. When switching R versions, it
helps to remove `~/.local/share/rstudio` (e.g. `rm -rf ~/.local/share/rstudio`) — it will be
regenerated.

Because the RStudio Server interface is a single-threaded process, a long-running operation can
make another action (such as saving) time out with **Status code 502**. Usually nothing crashes —
wait for the running code to finish and try again. If you exceed your requested time limit you'll
see **Status code 503**; start a new session from **Interactive Apps → RStudio Server**.

!!! warning "Quitting RStudio does not free the node"
    Selecting **File → Quit Session** (or the red icon in RStudio) only closes RStudio — it does
    **not** end your interactive HTC session, and you keep consuming CPU hours. To end it, return
    to the Dashboard and click the red **Delete** button.

## RStudio Server on GPU

[↑ Back to app menu](#apps)

RStudio Server on GPU is the same app running on a GPU node. Like Jupyter on GPU, it has no
pinned tile — open it from **Interactive Apps → RStudio Server on gpu** (under the **Deep
Learning** group).

![Interactive Apps menu open, with RStudio Server on gpu listed under the Deep Learning group](../_assets/img/web-portals/ondemand-rstudio-gpu-1.png)

On the launch form, set the job parameters and click **Launch**:

| Field | What it does |
| ----- | ------------ |
| R version | The R module to load (for example, `4.5.0`). |
| Number of hours | Wall-time limit for the session. |
| Number of cores | 1–64 cores on a GPU node, roughly 8 GB of memory per core unless you request a whole node. |
| Memory (GB) | Optional explicit memory request. |
| GPU type | Which GPU to request (for example, `l40s`). |
| constraint | A node feature to match (for example, `amd,40g`). |
| Number of gpu cards | 1–4 cards on the node. |
| Account | The Slurm allocation to charge; leave blank to use your default. |
| Reservation | Leave blank unless you've been given a reservation. |

![Top of the RStudio Server on gpu launch form: R version, cores, memory, and GPU type](../_assets/img/web-portals/ondemand-rstudio-gpu-2a.png)

![Bottom of the RStudio Server on gpu launch form: GPU type, constraint, GPU cards, account, and the Launch button](../_assets/img/web-portals/ondemand-rstudio-gpu-2b.png)

The session is queued, then runs; when it shows **Running**, click **Connect to RStudio Server**.

![My Interactive Sessions showing the RStudio Server on gpu session queued](../_assets/img/web-portals/ondemand-rstudio-gpu-3.png)

![My Interactive Sessions showing the RStudio Server on gpu session running on a GPU node, with a Connect to RStudio Server button](../_assets/img/web-portals/ondemand-rstudio-gpu-4.png)

![RStudio Server IDE running in the browser on a GPU node](../_assets/img/web-portals/ondemand-rstudio-gpu-5.png)

!!! note "This session is a Slurm job"
    An RStudio Server on GPU session is a Slurm job on a dedicated GPU node. From the RStudio
    **Terminal** tab, `squeue -M gpu -u $USER` lists it with its job ID, node, and partition.

![RStudio Terminal tab showing the session listed as a Slurm job on a GPU node by squeue](../_assets/img/web-portals/ondemand-rstudio-gpu-6.png)

The R modules bundle many R and Bioconductor packages. To install your own, open **Clusters →
HTC Shell Access**, load an R module (find the current one with `module spider r`), start `R`,
and use `install.packages("pkg")` or `BiocManager::install("Bioconductor_pkg")`. Use
`.libPaths()` to see where R searches.

### Common RStudio errors on GPU

RStudio Server stores its state in your home directory (`~/.local/share/rstudio` or the legacy
`~/.rstudio`), which has a 75 GB quota. Large project files or timed-out sessions can fill it; you
can delete unused sessions from `~/.local/share/rstudio/sessions`. When switching R versions, it
helps to remove `~/.local/share/rstudio` (e.g. `rm -rf ~/.local/share/rstudio`) — it will be
regenerated.

Because the RStudio Server interface is a single-threaded process, a long-running operation can
make another action (such as saving) time out with **Status code 502**. Usually nothing crashes —
wait for the running code to finish and try again. If you exceed your requested time limit you'll
see **Status code 503**; start a new session from **Interactive Apps → RStudio Server on gpu**.

!!! warning "Quitting RStudio does not free the node"
    Selecting **File → Quit Session** (or the red icon in RStudio) only closes RStudio — it does
    **not** end your interactive session, and you keep consuming your allocation. To end it, return
    to **My Interactive Sessions** and click the red **Delete** button.

## MATLAB

[↑ Back to app menu](#apps)

Two MATLAB apps are offered. Prefer **matlab** (the `matlab-proxy` web app); **MATLAB on htc** is
the older VNC-based version. Launch the web app from the **matlab** tile on the Dashboard.

![Open OnDemand Dashboard with the matlab pinned app](../_assets/img/web-portals/ondemand-matlab-1.png)

The launch form is simpler than the other apps' — just a version, wall time, and cores:

| Field | What it does |
| ----- | ------------ |
| Matlab version | The MATLAB release to load (for example, `R2025a`). |
| Number of hours | Wall-time limit for the session. |
| Number of cores | Cores on the node, roughly 8 GB of memory per core unless you request a whole node. |

![OnDemand matlab launch form: MATLAB version, hours, and cores](../_assets/img/web-portals/ondemand-matlab-2.png)

The session is queued, then runs; when it shows **Running**, click **Connect to Matlab**.

![My Interactive Sessions showing the matlab session queued](../_assets/img/web-portals/ondemand-matlab-3.png)

![My Interactive Sessions showing the matlab session running, with a Connect to Matlab button](../_assets/img/web-portals/ondemand-matlab-4.png)

MATLAB opens a **Status Information** panel while it starts, which can take a few minutes. It
shows the license source (Pitt's Network License Manager) and confirms authentication, with
controls to start, stop, or shut down the MATLAB process.

![MATLAB Status Information panel showing licensing, authentication, and session controls](../_assets/img/web-portals/ondemand-matlab-5.png)

Once MATLAB loads, a small control widget — the badge icon near the top — lets you restart or
sign out of MATLAB, and its color reflects the MATLAB status.

![MATLAB running in the browser, with the session-control widget tooltip explained](../_assets/img/web-portals/ondemand-matlab-6.png)

!!! warning "Shutting down MATLAB does not free the node"
    Stopping or shutting down MATLAB from the control widget ends the MATLAB process but not your
    interactive session. To release the node, return to **My Interactive Sessions** and click the
    red **Delete** button.

Other GUI applications — Stata, Mathematica, SAS, Cytoscape, QGIS, and more — launch the same way
from the **Interactive Apps** menu.

