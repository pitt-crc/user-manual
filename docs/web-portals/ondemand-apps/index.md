# Interactive Apps

Open OnDemand's **Interactive Apps** run graphical applications — Jupyter, RStudio, MATLAB, and
more — on a compute node with resources you request, all in your browser. This page explains how
launching one works; see each app's page for its specific options.

<div class="grid cards" markdown>

-   :material-notebook:{ .lg .middle } __Jupyter__

    ---

    Jupyter Notebook and JupyterLab, on CPU or GPU nodes.

    [:octicons-arrow-right-24: Jupyter](jupyter.md)

-   :material-chart-line:{ .lg .middle } __RStudio__

    ---

    RStudio Server, on CPU or GPU nodes.

    [:octicons-arrow-right-24: RStudio](rstudio.md)

-   :material-calculator-variant:{ .lg .middle } __MATLAB__

    ---

    The MATLAB desktop, served through the browser by `matlab-proxy`.

    [:octicons-arrow-right-24: MATLAB](matlab.md)

</div>

## Launching an app

Every interactive app follows the same steps:

1. Open the app from its **Pinned Apps** tile on the Dashboard, or from the **Interactive Apps**
   menu. GPU variants have no tile — find them under **Interactive Apps → Deep Learning**.
2. On the launch form, set the job parameters — typically the software version, number of hours,
   and number of cores (roughly 8 GB of memory per core), plus an optional **Account** (the Slurm
   allocation to charge). GPU apps add a GPU type and card count. Click **Launch**.
3. The session is **queued** while it waits for resources.
4. When it shows **Running**, click **Connect to …** to open the app in a new tab.

!!! tip "Don't over-request cores"
    Unless your code is genuinely parallel (for example, thread-parallelized NumPy) or
    memory-hungry, a few cores is plenty — larger requests wait longer in the queue.

![My Interactive Sessions showing a session queued](../../_assets/img/web-portals/ondemand-jupyter-3.png)

![My Interactive Sessions showing a session running, with a Connect button](../../_assets/img/web-portals/ondemand-jupyter-4.png)

!!! warning "Deleting is not optional"
    Closing the app's browser tab does **not** end the job. Until you click the red **Delete**
    button on **My Interactive Sessions**, the session keeps consuming your allocation.

## Interactive apps are Slurm jobs

Each session runs on a dedicated compute node with exactly the resources you requested — it is a
normal Slurm job, not something running in your browser or on a shared server. From a terminal
inside the app, `squeue -M <cluster> -u $USER` lists it with its job ID, node, and partition. This
is why a session waits in the queue, counts against your allocation, and must be ended with
**Delete**.

![Terminal inside JupyterLab showing the session listed as a Slurm job by squeue](../../_assets/img/web-portals/ondemand-jupyter-gpu-6.png)
