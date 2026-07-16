# Create a virtual environment for JupyterHub

Follow these steps to create a virtual environment for JupyterHub on the Teach cluster. The instructions below create
just the base environment with Python 3.11; if you need additional packages, install them yourself after creating the
environment. If you need a custom environment with specific packages preloaded into the JupyterHub dropdown menu, submit
a [help ticket](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and we'll create it for you.

**Step 1:** Log into any of the CRCD login nodes. For this example we'll use `htc.crc.pitt.edu`.

```
ssh <your_pitt_username>@htc.crc.pitt.edu
```

**Step 2:** Load the JupyterHub Conda module.

```
module load jupyterhub/hub.5.2.1
```

**Step 3:** Confirm the `conda` command is available.

```
conda --version
```

It should return something like `conda 25.7.0`.

**Step 4:** Create a new conda environment. This example creates one named `yk-teach-env` with Python 3.11.

```
conda create -p /path/to/your/env/yk-teach-env python=3.11 ipykernel jupyterlab
```

Replace `/path/to/your/env/` with the path where you want the environment. You can choose any name — here we use
`yk-teach-env` — but make it distinctive so it's easy to identify in the JupyterHub dropdown menu.

!!! warning "Keep `ipykernel` and `jupyterlab`"
    The `ipykernel` and `jupyterlab` packages are what let the environment run in JupyterHub, so don't remove them.

**Step 5:** Activate the new environment.

```
source activate /path/to/your/env/yk-teach-env
```

**Step 6:** Copy the following directory to a local directory of yours (for example `~/`) and change into the copy:

```
cp -r /software/rhel9/manual/install/jupyterhub/batchspawner ~/
cd ~/batchspawner
```

**Step 7:** From inside the `batchspawner` directory, install the package into the activated environment:

```
pip install .
```

**Step 8:** Install `jupyterhub` version `5.2.1` into the activated environment.

```
conda install jupyterhub=5.2.1
```

**Step 9:** Install any additional packages you need. For example, for `numpy` and `pandas`:

```
conda install numpy pandas
```

To install packages from PyPI instead, use the `pip` command:

```
pip install <package_name>
```

**Step 10:** Test your environment in JupyterHub. As described in the
[JupyterHub on the Teach Cluster](../getting-started/jupyter-teach.md) documentation, select it via the **Provide custom
path** option in the **Select Virtual Environment** dropdown when starting a new session.

**Step 11:** To let students use the environment, give them its path to enter in the JupyterHub dropdown menu:

```
/path/to/your/env/yk-teach-env
```
