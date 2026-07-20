# Jupyter on OnDemand

The CRCD offers Jupyter through Open OnDemand so users can run Python notebooks on the CRCD clusters — currently the
**HTC** and **GPU** clusters. The service is available to everyone with a CRCD account, through the OnDemand web portal
at [ondemand.htc.crc.pitt.edu](https://ondemand.htc.crc.pitt.edu).

As with any method of connecting to CRCD resources, first make sure you are connected to the
[GlobalProtect VPN](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=3426), then proceed with the steps
below.

When you visit the OnDemand web portal, you are prompted to log in with your Pitt account:

![OnDemand login prompt](../_assets/img/web-portals/ondemand_001.png)

Once logged in, you see the Dashboard:

![OnDemand dashboard](../_assets/img/web-portals/ondemand_002.png)

Click the **All Available Apps** link (highlighted in red above) to see every application available through OnDemand:

![All available apps](../_assets/img/web-portals/ondemand_003.png)

Search for "Jupyter" in the search box; two results appear:

![Jupyter search results](../_assets/img/web-portals/ondemand_004.png)

- **Jupyter** — launches Jupyter on the HTC cluster.
- **Jupyter on GPU** — launches Jupyter on the GPU cluster.

## Jupyter on HTC

Click the **Jupyter** link to open the submission form:

![Jupyter on HTC submission form](../_assets/img/web-portals/ondemand_006.png)

Several ready-to-use Python environments are offered for Jupyter on HTC:

- Python 3.10
- Python 3.9
- Python 3.8
- Python 3.7
- Python 2.7

![Python environment dropdown](../_assets/img/web-portals/ondemand_005.png)

These are base conda installations with JupyterLab integration, so you can start Jupyter and work with your notebooks
right away. You can also use your own conda environment by providing its path:

![Custom conda environment path](../_assets/img/web-portals/ondemand_007.png)

Or run Jupyter from a Singularity container by providing the container's path:

![Singularity container path](../_assets/img/web-portals/ondemand_008.png)

After selecting an environment, set the number of hours (default 1) and cores (default 1). If you belong to more than
one group/account, you can also choose which account the Service Units are drawn from:

![Hours, cores, and account](../_assets/img/web-portals/ondemand_009.png)

Click **Launch**. The job is queued:

![Job queued](../_assets/img/web-portals/ondemand_010.png)

Wait until the requested resources are allocated:

![Resources allocated](../_assets/img/web-portals/ondemand_011.png)

Click **Connect to Jupyter** to open the JupyterLab interface, where you can browse your home directory for notebooks:

![JupyterLab interface](../_assets/img/web-portals/ondemand_012.png)

## Jupyter on GPU

Click the **Jupyter on GPU** link to open the submission form:

![Jupyter on GPU submission form](../_assets/img/web-portals/ondemand_013.png)

Several ready-to-use deep-learning environments are offered for Jupyter on GPU, for example:

- PyTorch 2.0.0
- PyTorch 1.11.0
- PyTorch 1.8.1 + CUDA 11.1.1
- TensorFlow 2.4.1 + CUDA 10.1

![Deep-learning environment dropdown](../_assets/img/web-portals/ondemand_013_.png)

!!! tip "Check the current offerings"
    The exact framework and CUDA versions in the dropdown change over time — use whatever the form currently lists
    rather than the specific versions shown here.

These are base conda installations with JupyterLab integration and GPU-enabled packages. As on HTC, you can instead
supply the path to your own conda environment:

![Custom conda environment path (GPU)](../_assets/img/web-portals/ondemand_014.png)

Or run from a Singularity container:

![Singularity container path (GPU)](../_assets/img/web-portals/ondemand_015.png)

After selecting an environment, you can choose the CUDA version to load with it:

![CUDA version selection](../_assets/img/web-portals/ondemand_016.png)

You also choose the **GPU type**, which determines how many CPU cores are available per GPU card:

- A100: 12 cores per GPU card
- A100_NVLINK: 16 cores per GPU card
- A100_multi: 16 cores per GPU card

!!! note "Available GPU types reflect current hardware"
    The GPU types listed in the form track the GPU cluster's current partitions. See the
    [GPU cluster](../hardware_profiles/gpu.md) page for the full, up-to-date list (such as `l40s`, `rtx6k`, and `h200`)
    and confirm the options in the live dropdown, as they change as hardware is added or retired.

If you belong to more than one group/account, you can choose which account the Service Units are drawn from. After
choosing your environment and parameters, click **Launch**:

![Job queued (GPU)](../_assets/img/web-portals/ondemand_017.png)

Wait until the requested resources are allocated:

![Resources allocated (GPU)](../_assets/img/web-portals/ondemand_018.png)

Click **Connect to Jupyter** to open the JupyterLab interface:

![JupyterLab interface (GPU)](../_assets/img/web-portals/ondemand_019.png)

## General notes

!!! note "Project storage isn't browsable by default"
    Project storage locations (for example `/ix`, `/ix1`, and `/vast`) are not browsable from OnDemand by default. To
    reach them, create a symbolic link from your home directory to the location, for example:

    ```bash
    ln -s /ix/<group>/<username>/dir /ihome/<group>/<username>/ix_dir
    ```

You can view the logs of your Jupyter session by clicking **My Interactive Sessions**:

![My Interactive Sessions](../_assets/img/web-portals/ondemand_020.png)

This opens a file explorer with your session logs and scripts:

![Session logs and scripts](../_assets/img/web-portals/ondemand_022.png)

These logs are useful for debugging a session, and helpful to us when troubleshooting if you submit a support ticket.

## Using custom virtual environments

See the [Python environments](../applications/python.md) page for a complete guide to managing Python environments on
the CRCD clusters. In short:

1. To use a custom environment (rather than one of the pre-installed modules), create the environment first using one
   of the methods in that guide and install your required packages into it.
2. Make sure you install the `jupyter` and `jupyterlab` packages into the environment so it can run Jupyter through
   OnDemand (via `conda install`, `mamba install`, or `pip install`).
3. If you need Lmod modules loaded during your Jupyter on OnDemand session, install the `jupyterlmod` package, which
   adds a panel to Jupyter's interface for selecting modules to load. This package can only be installed through `pip`.

![type:video](../_assets/img/web-portals/7.install_jupyterlmod.mp4)

![type:video](../_assets/img/web-portals/jupyter_ondemand.mp4)
