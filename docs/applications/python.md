# Python

The CRC provides a variety of pre-installed Python versions that can be accessed immediately through Lmod. Users can select from the available Python versions or install a different version if their target pipeline has specific requirements. A full list of available Python versions through Lmod can be viewed using:
    
```
module spider python
```
![type:video](../_assets/img/applications/1.modules_info.mp4)

All available modules are Anaconda distributions of Python that support pip and conda installation commands. Users do not have privileges to install Python packages to the system, so keep these options in mind when you are setting up your environment:

1.  Manage your python package installation environment using a conda virtual environment (recommended). You can create any number of virtual environments for your various projects, and they are great for preventing dependency conflicts when switching between workflows that use different software tools.
2.  ```pip install --user package_name``` will install a Python package directly into a directory in your /ihome location. You have full permissions to access these files/directories. Be wary of using this method as it is easier to run into the dependency conflicts mentioned above. When using pip, if you are unsure of what version of the package you want to install you can use pip install [package_name]== to print the available versions.

Creating a virtual environment after loading Python via the module system:
--------------------------------------------------------------------------

1.  Load a Python module from Lmod, for general use we recommend a latest Anaconda distribution such as:
    
    You can use ```module spider python/ondemand-jupyter-python3.10```  to generate the prerequisite modules needed to load ahead of loading this particular module.
    
    *   python/anaconda3.9-2021.11
    *   python/ondemand-jupyter-python3.10
    
    ![type:video](../_assets/img/applications/1.module_spider.mp4)
    
2.  Create a virtual environment via the conda command, and please note that Anaconda creates virtual environments in your home directory by default (iHome: ihome/<your group name>/<your username>/.conda/envs/my_env) which is limited in space for all CRC users, so we recommend that you provide a prefix while creating the environment so that Anaconda creates the environment elsewhere where you have more storage available such as IX. You can also specify the Python version you want to provision for the environment using the option python=3.8
    
    ```
    conda create --prefix=/ix/<your group name>/<your user name>/envs/my_env python=3.9
    ```
    ![type:video](../_assets/img/applications/2.create_env.mp4)
3.  Activate your newly created virtual environment through the following command:
    
    ```
    source activate /ix/<your group name>/<your user name>/envs/my_env
    ```
4.  You can now install packages into your new environment through conda install or pip install as follows:
    
    ``` 
    conda install -p /ix/<your group name>/<your user name>/envs/my_env numpy
    pip install scipy
    ```
    However, please note that to use , you need to have your virtual environment activated as indicated in step #3
    ![type:video](../_assets/img/applications/3.activate_install.mp4)
    
5.  Important notes to consider for using this method to create and manage your environment:
    *   Please use source activate to activate your environment instead of conda activate. As the later will prompt you to issue the command conda init, which is not recommended and might cause conflicts.
    *   When you load one of our Anaconda modules, you have effectively loaded the base conda environment for that Anaconda version. However, conda tries to manage activation of its own base environment by modifying the user's .bashrc file.
    *   Even if you have installed your own local version of Anaconda or miniconda, **DO NOT USE** conda init. When conda init runs, it places commands into your .bashrc file that will stop certain things from working on the system. Many modules use their own python environments. Activating a specific conda environment in your .bashrc file can break these.
6.  Workaround for using conda activate:  
    The conda init command places code in your .bashrc file that modifies, among other things, the PATH environment variable by prepending it to the path of the base conda environment. This occurs before the default system modules are loaded. Other modules may also have libraries that will hide Anaconda libraries and cause errors.  
    If you must utilize in conda activate local Anaconda or miniconda installations:
    *   Run conda init, and then immediately open .bashrc with a file editor.
    *   Remove the code that was added by conda init and place it in another script file (for example, conda_init.sh).
    *   After the login process completes, run the code in the script file: source conda_init.shYou should now be able to use conda activate.

Install packages into your virtual environment:
-----------------------------------------------

*   Install packages with conda:
    *   The conda package manager is recommended for maintaining your environment.
    *   Take look at and use the Conda [cheat sheet](https://docs.conda.io/projects/conda/en/latest/user-guide/cheatsheet.html),  which lists the most commonly used commands. More detailed documentation is in the [Conda User Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/index.html).
    *   To install a new package, run:
        
        ```
        conda install -n my_env package_name
        ```
    *   To install a specific version (x.y.z) of a package:
        
        ```
        conda install -n my_env package_name=x.y.z 
        conda install -n my_env 'package_name>=x.y.z' 
        conda install -n my_env 'package_name<=x.y.z' 
        ```
    *   Conda channels are the remote repository that conda takes to search or download the packages. If you want to install a package that is not in the default Anaconda channel, you can tell conda which channel containing the package, so that conda can find and install. To install a new package from bioconda channel, run:
        
        ```
        conda install -n my_env -c channel package_name
        ```
*   Install packages with pip:
    *   When a Python package does not exist as a conda package, one can use the Python pip installer. We recommend using pip only as a last resort since this way one loses the flexibility of the conda packaging environment (automatic conflict resolution and version upgrade/downgrade).
    *   Make sure that your environment is activated before using the pip command, or you can point to the command from your environment bin folder.
    *   To install a module using pip, run:
        
        ```
        pip install package_name 
        /ix/<your group name>/<your user name>/envs/my_env/bin/pip install package_name
        ```
    *   To install a specific version (x.y.z) of a package:
        
        ```
        pip install package_name==x.y.z
        pip install package_name>=x.y.z
        pip install package_name<=x.y.z
        ```

Creating a virtual environment using your customized conda installation:
------------------------------------------------------------------------

1.  Explore and download the right conda version for your application (either miniconda or anaconda) from [conda download page](https://conda.io/projects/conda/en/stable/user-guide/install/download.html).
2.  For this tutorial we will use the latest miniconda:
   
    ``` 
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    ```
3.  Execute the installer using:
    
    ```
    bash Miniconda3-latest-Linux-x86_64.sh
    ```
4.  The installation will ask you to press ENTER to continue and review the license agreement and prompt you for accepting the terms of the agreement by entering yes.
5.  By default conda installation is palced in your home directory (iHome) which is not recommended because it can consume your limited home allocation. After accepting the license agreement, the installer will ask you if you want a different installation path other than your home directory and you can enter the path where you have space available (e.g., IX) as follows: /ix/<your group name>/<your user name>/custom_miniconda
6.  The installer will start unpacking conda files into the specified installation path.
7.  After finishing unpacking, the installer will prompt you choose if you want to run conda init, which is not recommended as mentioned earlier.
    ![type:video](../_assets/img/applications/4.custom_conda.mp4)
8.  Sometimes conda environment solution is slow especially for channels such as conda-forge, so here is an extra step that is OPTIONAL but provides a faster environment solution which is fully compatible with conda packages. You can install mamba into conda base environment which will offer a better environment solution by this command:
    
    ```
    /ix/<your group name>/<your user name>/custom_miniconda/bin/conda install mamba -n base -c conda-forge
    ```
9.  You can create your environment now via the conda command similar to the steps mentioned earlier but with the prefix to your custom conda installation:
    
    ```
    ix/<your group name>/<your user name>/custom_miniconda/bin/conda create -n my_env
    ```
10.  No need to add a prefix for the environment name because this custom conda installation will directly place the new environment within the installation directory: ix/<your group name>/<your user name>/custom_miniconda/envs/
11.  Now you can install packages into your environment through conda or mamba as follows:
    
    ```
    /ix/<your group name>/<your user name>/custom_miniconda/bin/conda install -n my_env -c channel package_name
    /ix/<your group name>/<your user name>/custom_miniconda/bin/mamba install -n my_env -c channel package_name
    ```
12.  You can activate your environment through the following command:
    
    ```
    source /ix/<your group name>/<your user name>/custom_miniconda/bin/activate my_env
    ```
    ![type:video](../_assets/img/applications/5.custom_conda_install.mp4)
13.  You can also use pip to install packages into your environment when the requested package is not available through conda/mamba, but you have to make sure that your environment is activated first or to pint to pip executable of the environment as follows:
    
    ```
    pip install package_name
    /ix/<your group name>/<your user name>/custom_miniconda/envs/my_env/bin/pip install package_name
    ```
    ![type:video](../_assets/img/applications/6.custom_conda_install_pip.mp4)

Using custom virtual environments with Jupyter on OnDemand:
-----------------------------------------------------------

1.  To run Jupyter via OnDemand you multiple options of pre-installed modules that you can load and use; however if you want to use your custom environment, you need to create the environment first through one of the methods mentioned earlier and install your required packages into it.
2.  You must make sure that you install jupyter and jupyterlab packages into your environment so that you can run Jupyter through OnDemand (you can use conda install, mamba install or pip install to install these packages like mentioned earlier in the steps of creating environments).
3.  If you need other modules (from Lmod) to be loaded during your Jupyter on OnDemand session, you have to install a package called jupyterlmod which adds a part to Jupyter’s interface where you can specify the modules you need to load. Please note that this package can only be installed only through pip.
    ![type:video](../_assets/img/applications/7.install_jupyterlmod.mp4)
    ![type:video](../_assets/img/applications/jupyter_ondemand.mp4)

Running Slurm jobs using your virtual environment:
--------------------------------------------------

Here are sample batch files for creating a Slurm job using your newly installed virtual environment depending on the way you created the environment:

1.  If you used an Lmod python module to create your environment, then you must follow the following format (the same python module you used for creating the environment must be used):
    
    ```
    #!/bin/bash
    #SBATCH --job-name=conda
    #SBATCH -N 1
    #SBATCH -t 3-00:00 
    #SBATCH --cpus-per-task=1 
      
    #Load python via LMOD 
    module load python/ondemand-jupyter-python3.10 
      
    #Activate your environment 
    source activate /ix/<your group name>/<your user name>/envs/my_env 
      
    #Run commands utilizing your loaded Python tool 
    python xyz.py
    ```
2.  If you used a custom conda installation, then you must follow the following format:
    
    ```
    #!/bin/bash
    #SBATCH --job-name=conda
    #SBATCH -N 1
    #SBATCH -t 3-00:00
    #SBATCH --cpus-per-task=1
     
    #Load other modules that you need
     
    #Activate your environment
    source /ix/<your group name>/<your user name>/custom_miniconda/bin/activate my_env
    #Run commands utilizing your loaded Python tool
    python xyz.py
    ```

Custom Miniconda Environment as an Lmod Module
----------------------------------------------

Another way to utilize your custom conda environment, is to create an Lmod module for that environment which can be loaded easily using Lmod at the beginning of your Slurm scripts.

1.  You have to create a custom conda installation as mentioned in the previous sections.
2.  Create a directory where the user environment module hierarchy will reside, and then copy our miniconda module file to this directory. This directory can be anywhere (e.g. your home directory)
3.  Edit the copied module file using any editor and change the "local package_root" parameter into your environment location. Here we will use the previously configured virtual environment (custom_miniconda).
    
    ```
    cd ~
    mkdir modulefiles
    cd modulefiles
    cp /ihome/crc/modules/Core/python/ondemand-jupyter-python3.10.lua custom_miniconda.lua
    vi my_env.lua  #edit and change my_env.lua: local package_root = "/ix/<your group name>/<your user name>/custom_miniconda"
    ```
4.  The user module environment (the directory created in the previous step) must be loaded into the default module environment with the module use command.
5.  Now, you can load your virtual environment module as you do with other CRC modules:
    
    ```
    module purge
    module use ~/modulefiles
    module load custom_miniconda
    which conda 
    ```
    ![type:video](../_assets/img/applications/custom_conda_module.mp4)

Machine/Deep Learning Pipelines
-------------------------------

### Pytorch on GPU environment

1.  To create a virtual environment that encapsulates Pytorch and works on GPU resources of the CRC, you have to follow the official installation instructions provided here, to make sure that all dependencies are installed such as CudaToolkit and CuDNN: [https://pytorch.org/get-started/previous-versions/](https://pytorch.org/get-started/previous-versions/)
2.  Create a virtual environment using any of the methods demonstrated earlier and name it “pytorch_gpu”.
3.  Choose the version of pytorch you need CPU/GPU along with the necessary packages. For demonstration we chose Pytorch version 2.0.0 for GPU with Pytorch-CUDA version 11.8
4.  If you will be using this environment with OnDemand, you need to install Jupyter and JupyterLab packages as well.
5.  You can add more packages into the environment as needed but be mindful of the version dependencies between packages and use conda install rather than pip install whenever possible.
    ![type:video](../_assets/img/applications/pytorch_gpu.mp4)

### Tensoflow on GPU environment

1.  To create a virtual environment that encapsulates Tensorflow and works on GPU, we will follow the official installation instructions provided here: [https://www.tensorflow.org/install/pip](https://www.tensorflow.org/install/pip)
2.  Create a virtual environment using conda create with a python version of 3.9
    
    ```
    conda create -n tf_gpu python=3.9
    ```
3.  Activate the environment using one of the methods mentioned earlier:
    
    ```
    source custom_miniconda/bin/activate tf_gpu
    ```
4.  Install cudatoolkit and nvidia-cudnn with versions as specified in the instructions:
    
    ```
    conda install -c conda-forge cudatoolkit=11.8.0
    pip install nvidia-cudnn-cu11==8.6.0.163
    ```
5.  This step is different from what is mentioned in the official guide but it serves the same purpose of making cudnn library files visible to the system. Create symbolic links of cudnn library files from cudnn library path (/path_to_envs/tf_gpu/lib/ python3.9/site-packages/nvidia/cudnn/lib/) to the environment library path (/path_to_envs/tf_gpu/lib/):
    
    ```
    ln -s /path_to_envs/tf_gpu/lib/ python3.9/site-packages/nvidia/cudnn/lib/libcudnn* /path_to_envs/tf_gpu/lib/
    ```
6.  Upgrade pip:
    
    ```
    pip install --upgrade pip
    ```
7.  Install Tensorflow:
    
    ```
    pip install tensorflow==2.12.*
    ```
8.  Install cuda-nvcc:
    
    ```
    conda install -c nvidia cuda-nvcc=11.3.58
    ```
9.  Create the following directory hierarchy in the environment library path (/path_to_envs/tf_gpu/lib):
    
    ```
    mkdir -p /path_to_envs/tf_gpu/lib/nvvm/libdevice
    ```
10.  Copy “libdevice.10.bc” from (/path_to_envs/tf_gpu/lib) to the directory created in the previous step:
    
    ```
    cp /path_to_envs/tf_gpu/lib/ibdevice.10.bc /path_to_envs/tf_gpu/lib/nvvm/libdevice/
    ```
11.  Please define the following environment variable in slurm job file or at the beginning of your notebook/python script so that training/prediction works flawlessly on tensorflow-gpu:
    
    ```
    export XLA_FLAGS=--xla_gpu_cuda_data_dir=/path_to_envs/tf_gpu/lib/
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH: /path_to_envs/tf_gpu/lib/
    ```
    ![type:video](../_assets/img/applications/tf_gpu_1.mp4)
12.  You can test that Tensorflow is really working on GPU by listing the visible GPU devices as follows (first you have to make sure that you are on a machine with GPUs, and that’s why I request an interactive GPU session on the demo video):
    
    ```
    python3 -c "import tensorflow as tf; print(tf.config.list_physical_devices('GPU'))"
    ```
    ![type:video](../_assets/img/applications/tf_gpu_2.mp4)

Alternate Instructions for Virtual Environments with virtualenvwrapper
----------------------------------------------------------------------

We strongly encourage using virtual environments, which give you complete control over which versions of packages are installed. Please note that virtualenvwrapper is no longer tested with newer python versions (after Python 3.6) and will be deprecated from the CRC's software soon. virtualenvwrapper is only available for the following Python modules:

1.  python/anaconda3.5-4.2.0
2.  python/anaconda3.6-5.2.0
3.  python/3.7.0

Here is an example of installing PyTorch (CPU Version) into a Virtual Environment using (only supported by ) virtualenvwrapper:

```
$ module load python/3.7.0 venv/wrap
$ mkvirtualenv pytorch
$ workon pytorch
$ pip install numpy torch torchvision
$ python
Python 3.7.0 (default, Jun 28 2018, 13:15:42)
[GCC 7.2.0] :: Anaconda, Inc. on linux 
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> x = torch.rand(5, 3)
>>> print(x) tensor([[0.6022, 0.5194, 0.3726],
                                   [0.0010, 0.7181, 0.7031],
                                   [0.7442, 0.5017, 0.2003],
                                   [0.1068, 0.4622, 0.2478],
                                   [0.8989, 0.8953, 0.0129]])
>>>
```

Anaconda configuration
----------------------

The conda configuration file, .condarc, is an optional runtime configuration file that allows advanced users to configure various aspects of conda, such as which channels it searches for packages, proxy settings, and environment directories.

The following displays a sample .condarc file, where the path of caching packages is defined. You can change this path in order to direct package caching into a different path to save your iHome space which is by default where the caching happens
```
[yak73@login0a yak73]$ cat ~/.condarc 
pkgs_dirs:
- /ihome/sam/yak73/.conda/pkgs
[yak73@login0a yak73]$
```
