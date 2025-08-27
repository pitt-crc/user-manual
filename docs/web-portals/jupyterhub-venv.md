# Create a virtual environment for JupyterHub

Please follow the following steps to create a virtual environment for JupyterHub on the TEACH cluster. The instructions
provided here show how to create just the base environment with Python 3.11. If you need additional packages, you have
to install them yourself after creating the environment. If you need a custom environment with specific packages that
can be preloaded in JupyterHUb dropdown menu, please submit a
[help ticket](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and we will create it for you.

**Step1:** Log into any of the login nodes of the CRCD clusters. For this example we will use login3 "htc.crc.pitt.edu".

   ```bash
   ssh <your_pitt_username>@htc.crc.pitt.edu
   ```

**Step2:** Load the JupyterHub Conda module.

   ```bash
   module load jupyterhub/hub.5.2.1
   ```

**Step3:** Test if you have `conda` command available.

   ```bash
   conda --version
   ```

It should return something like `conda 25.7.0`.

**Step4:** Create a new conda environment. In this example we will create an environment named `yk-teach-env` with
Python 3.11.

   ```bash
   conda create -p  /path/to/your/env/yk-teach-env python=3.11 ipykernel jupyterlab
   ```
Replace `/path/to/your/env/` with the actual path where you want to create the environment. You can choose any name for 
the environment, here we used `yk-teach-env` as an example, but please try to make it distinctive so that it can be
easily identified in the JupyterHub dropdown menu. The `ipykernel` and `jupyterlab` packages are included to ensure that
the environment can be used in JupyterHub, so please do not remove them.

**Step5:** Activate the newly created environment.

   ```bash
   source activate /path/to/your/env/yk-teach-env
   ```

**Step6:** Copy the following dir to a local dir of yours (e.g. `~/`) and change the dir to this copied dir:

   ```bash
   cp -r /software/rhel9/manual/install/jupyterhub/batchspawner ~/
   cd ~/batchspawner
   ```

**Step7:** Make sure you are in the `batchspawner` dir and install the package in the activated environment:

   ```bash
   pip install .
   ```

**Step8:** Install `jupyterhub` version `5.2.1` in the activated environment.

   ```bash
   conda install jupyterhub=5.2.1
   ```

**Step9:** Install any additional packages you need in the environment. For example, if you need `numpy` and
`pandas`, you can install them as follows:

   ```bash
   conda install numpy pandas
   ```
You can install any other packages you need in the same way. If you need to install packages from PyPI, you can use 

`pip` command as follows:

   ```bash
   pip install <package_name>
   ```

**Step10:** You can now test your environment by using it in JupyterHub. As referred in the
[JupyterHub on the Teach Cluster](../snippets/jupyter-teach.md) documentation, you can use your environment in the
**Select Virtual Environment** dropdown menu (the Provide custom path option) when starting a new JupyterHub session.

**Step11:** You can provide the path to your environment for students to use in the JupyterHub dropdown menu as follows:

   ```
   /path/to/your/env/yk-teach-env
   ```
