# Interactive / Remote Computing with VS Code

This tutorial outlines how to set up VS Code for interactive/remote development/debugging on Pitt CRC computing nodes.

## Prerequisites

- The latest version of VS Code installed on your local machine
- Latest version of the ["Remote Development" extension pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

## Steps performed **only once**

Add the following lines to the ssh config file on your local machine (~/.ssh/config) 
and replace <name> with your Pitt username:

```
Host htc  
  ControlMaster auto  
  ControlPath ~/.ssh/master-%r@%h:%p  
  HostName htc.crc.pitt.edu  
  User <name>

Host htcx  
  ProxyCommand ssh htc 'nc $(squeue --me --name=tunnel --states=R -h -O NodeList,Comment)'  
  StrictHostKeyChecking no  
  User <name>
```

For users on a **Windows** machine, the ControlMaster may not be available, so add the following lines instead:

```
Host htc
  HostName htc.crc.pitt.edu
  User <name>

Host htcx  
  ProxyCommand ssh htc "nc $(squeue --me --name=tunnel --states=R -h -O NodeList,Comment)"  
  StrictHostKeyChecking no  
  User <name>
```

Connect to the HTC cluster and create the following sbatch file into your home directory (`~/tunnel.sbatch`):

```shell
#!/bin/bash

#SBATCH --output="tunnel.log"  
#SBATCH --job-name="tunnel"  
#SBATCH --time=4:00:00 # walltime  
#SBATCH --cpus-per-task=2 # number of cores  
#SBATCH --mem-per-cpu=8G # memory per CPU core

# load the modules  
module load python/ondemand-jupyter-python3.8

# find open port  
PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0)); print(s.getsockname()[1]); s.close()')  
scontrol update JobId="$SLURM_JOB_ID" Comment="$PORT"

# start sshd server on the available port  
echo "Starting sshd on port $PORT"  
/usr/sbin/sshd -D -p ${PORT} -f /dev/null -h ${HOME}/.ssh/id_rsa
```

In the terminal on your local machine, generate an ssh key if you don't have one already using the following:
```
ssh-keygen -t rsa
```
Copy your local public ssh key to the cluster using:
```
ssh-copy-id htc
```
On `htc.crc.pitt.edu`, run the following commands if you have not done so:
```
ssh-keygen #then follow on screen instructions  
cd ~/.ssh  
cat id_rsa.pub >> authorized_keys
```

## Steps performed every time to connect your VS Code to the cluster
---------------------------------------------------------------------

From your local terminal, connect to the cluster using ssh htc and once logged in, 
type sbatch tunnel.sbatch to start the remote server. Make sure that your job does run!

Sample output:

```commandline
[user@login0b ~]$ sbatch tunnel.sbatch  
Submitted batch job 1383495  
[user@login0b ~]$ squeue -u user

  JOBID      PARTITION      NAME      USER      ST      TIME      NODES      NODELIST(REASON)  
1383495            htc    tunnel      user       R      0:17          1           htc-1024-n0
```

Open VS Code on your local machine and connect to your projects using `Remote Explorer` with `htcx` as the ssh target.

## Changes you need to do to allocate resources with GPUs
----------------------------------------------------------

Add the following host to the ssh config file on your local machine (`~/.ssh/config`) and 
replace `<name>` with your username:
```
Host gpux  
  ProxyCommand ssh htc 'nc $(squeue -M gpu --me --name=tunnel --states=R -h -O NodeList,Comment)'  
  StrictHostKeyChecking no  
  User <name>
```

Change the contents of the sbatch file in your home directory on HTC (`~/tunnel.sbatch`) to:
```shell
#!/bin/bash  
#SBATCH --output="tunnel.log"  
#SBATCH --job-name="tunnel"  
#SBATCH --cpus-per-task 2  
#SBATCH --time 0-01:00:00  
#SBATCH --clusters=gpu  
#SBATCH --partition=a100  
#SBATCH --gres=gpu:1

module load python/ondemand-jupyter-python3.8 # load the modules

# find open port  
PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0));  
print(s.getsockname()[1]); s.close()')  
scontrol update JobId="$SLURM_JOB_ID" Comment="$PORT"  
# start sshd server on the available port  
echo "Starting sshd on port $PORT"  
/usr/sbin/sshd -D -p ${PORT} -f /dev/null -h ${HOME}/.ssh/id_rsa
```
