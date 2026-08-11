# Jupyter

The **Jupyter** app runs Jupyter Notebook or JupyterLab on a compute node. (This is the Jupyter
app inside Open OnDemand — distinct from the standalone JupyterHub portal at
`jupyter.crc.pitt.edu`.) For the general launch → connect → delete flow, see
[Interactive Apps](index.md).

## Jupyter (CPU)

Launch it from the **Jupyter** tile on the Dashboard, or from **Interactive Apps → Jupyter**.

![Open OnDemand Dashboard with the Jupyter pinned app highlighted](../../_assets/img/web-portals/ondemand-jupyter-1.png)

Set the launch-form fields and click **Launch**:

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

![Top of the Jupyter launch form: cluster, JupyterLab option, and Python version](../../_assets/img/web-portals/ondemand-jupyter-2a.png)

![Bottom of the Jupyter launch form: cores, memory, account, and the Launch button](../../_assets/img/web-portals/ondemand-jupyter-2b.png)

When the session is running, **Connect to Jupyter** opens the interface in a new tab.

![JupyterLab running in the browser with the file browser and a notebook open](../../_assets/img/web-portals/ondemand-jupyter-5.png)

## Jupyter on GPU

Jupyter on GPU is the same app running on a GPU node. It has no pinned tile — open it from
**Interactive Apps → Jupyter on gpu** (under the **Deep Learning** group).

![Interactive Apps menu open, with Jupyter on gpu listed under the Deep Learning group](../../_assets/img/web-portals/ondemand-jupyter-gpu-1.png)

The form adds GPU-specific fields to the ones above:

| Field | What it does |
| ----- | ------------ |
| Python version | Bundles the framework, Python, and CUDA together — for example, `pytorch 2.5.1 python 3.11 cuda 12.4`. |
| Cuda version | A separate CUDA module to load. Choose **None** if you're using PyTorch with its bundled CUDA packages. |
| GPU type | Which GPU/node type to request (for example, `rtx6k,96g,amd(avx512)`). |
| Number of gpu cards | 1–4 cards on the node. |
| Reservation | Leave blank unless you've been given a reservation. |

![Top of the Jupyter on gpu launch form: JupyterLab option, Python/framework version, and CUDA version](../../_assets/img/web-portals/ondemand-jupyter-gpu-2a.png)

![Bottom of the Jupyter on gpu launch form: GPU type, number of GPU cards, account, and the Launch button](../../_assets/img/web-portals/ondemand-jupyter-gpu-2b.png)

![JupyterLab running in the browser on a GPU node](../../_assets/img/web-portals/ondemand-jupyter-gpu-5.png)
