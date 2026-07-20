# RStudio

RStudio Server runs the RStudio IDE in the browser on a compute node, so you can run more
compute-intensive R work than on a login node. For the general launch → connect → delete flow,
see [Interactive Apps](index.md).

## RStudio (CPU)

Launch it from the **RStudio Server 2026** tile on the Dashboard, or from
**Interactive Apps → RStudio Server**.

![Open OnDemand Dashboard with the RStudio Server 2026 pinned app](../../_assets/img/web-portals/ondemand-rstudio-1.png)

Set the launch-form fields and click **Launch**:

| Field | What it does |
| ----- | ------------ |
| R version | The R module to load (for example, `4.6.0`). |
| Number of hours | Wall-time limit for the session. |
| Number of cores | 1–128 cores, roughly 8 GB of memory per core unless you request a whole node. |
| Memory (GB) | Optional explicit memory request. |
| Account | The Slurm allocation to charge; leave blank to use your default. |
| Reservation | Leave blank unless you've been given a reservation. |

![Top of the RStudio Server launch form: R version, hours, and cores](../../_assets/img/web-portals/ondemand-rstudio-2a.png)

![Bottom of the RStudio Server launch form: memory, account, reservation, and the Launch button](../../_assets/img/web-portals/ondemand-rstudio-2b.png)

When the session is running, **Connect to RStudio Server** opens the IDE in a new tab.

![RStudio Server IDE running in the browser](../../_assets/img/web-portals/ondemand-rstudio-5.png)

The R modules bundle many R and Bioconductor packages. To install your own, open **Clusters →
HTC Shell Access**, load an R module (find the current one with `module spider r`), start `R`, and
use `install.packages("pkg")` or `BiocManager::install("Bioconductor_pkg")`. Use `.libPaths()` to
see where R searches.

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
    **not** end your interactive session, and you keep consuming your allocation. To end it, return
    to **My Interactive Sessions** and click the red **Delete** button.

## RStudio Server on GPU

RStudio Server on GPU is the same app running on a GPU node. Like Jupyter on GPU, it has no pinned
tile — open it from **Interactive Apps → RStudio Server on gpu** (under the **Deep Learning**
group).

![Interactive Apps menu open, with RStudio Server on gpu listed under the Deep Learning group](../../_assets/img/web-portals/ondemand-rstudio-gpu-1.png)

The form adds GPU-specific fields to the ones above:

| Field | What it does |
| ----- | ------------ |
| Number of cores | 1–64 cores on a GPU node — fewer than the CPU app's 1–128. |
| GPU type | Which GPU to request (for example, `l40s`). |
| constraint | A node feature to match (for example, `amd,40g`). |
| Number of gpu cards | 1–4 cards on the node. |

![Top of the RStudio Server on gpu launch form: R version, cores, memory, and GPU type](../../_assets/img/web-portals/ondemand-rstudio-gpu-2a.png)

![Bottom of the RStudio Server on gpu launch form: GPU type, constraint, GPU cards, account, and the Launch button](../../_assets/img/web-portals/ondemand-rstudio-gpu-2b.png)

![RStudio Server IDE running in the browser on a GPU node](../../_assets/img/web-portals/ondemand-rstudio-gpu-5.png)

The package-installation tip and the error and session-ending notes above apply here too. You can
confirm the session is a Slurm job from the RStudio **Terminal** tab with `squeue -M gpu -u $USER`.
