
I have pulled the latest Ollama image (0.9.2) on our cluster and you need to follow these steps to run and use Ollama:

1. Run the Ollama server using sbatch:

sbatch /software/build/ollama/ollama_0.9.2_a100_80gb.slurm

sbatch /software/build/ollama/ollama_0.9.2_l40s.slurm
[fangping@login3 ~]$ squeue -M gpu -u fangping
CLUSTER: gpu
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           1230409      l40s ollama_0 fangping  R       0:03      1 gpu-n55

2. This will run the ollama service on a GPU node with 125GB of memory, 16 cores and an A100 80GB GPU which should be suitable for all models provided by Ollama (You can run on L40S-48GB if you don't need A100 80GB by doing "sbatch /software/build/ollama/ollama_0.9.2_a100_80gb.slurm"). Once the job is submitted and running you inquire the host name and the port number it's listening on via the following command:

[fangping@login3 ~]$ squeue -M gpu --me --name=ollama_0.9.2_server_job --states=R -h -O NodeList,Comment
gpu-n55             45141

squeue -M gpu --me --name=ollama_0.9.2_server_job --states=R -h -O NodeList,Comment
 which will produce something like this if the job starts (or empty response if it's waiting in the queue):
 gpu-n66    12345

3. In the many cases you will be using this service you have to make sure that you set the following option to be able to connect to the running service you launched in the previous steps:

in case of rollama:

> library(rollama)
> options(rollama_server = "http://gpu-n66:12345")
> query("why is the sky blue?")
In case of Jupyter Python, you need also to define the Ollama host as mentioned for rollama before you query the models. 

[fangping@login3 ~]$ cd ~/.ollama
[fangping@login3 .ollama]$ ls -l
total 53
-rw------- 1 fangping sam 387 Jun  9 10:15 id_ed25519
-rw-r--r-- 1 fangping sam  81 Jun  9 10:15 id_ed25519.pub
drwxr-xr-x 4 fangping sam  50 Jul  1 12:27 models

[fangping@login3 ~]$ squeue -M gpu -u fangping
CLUSTER: gpu
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           1230409      l40s ollama_0 fangping  R      13:33      1 gpu-n55
[fangping@login3 ~]$ scancel -M gpu 1230409


4. To pull new models:

You have to make sure first to get an interactive session on SMP to run the image as follows:

srun -M smp -p smp -n4 --mem=16G -t0-04:00:00 --pty bash

If you are already in an interactive session on SMP or HTC, then you can just run the following directly from the seesion but make sure you don't run this on the login node or it will be killed,
- when you get directed to the node:

$ module load singularity/3.9.6
$ singularity shell /software/build/ollama/ollama_0.9.2.sif
Singularity$ export OLLAMA_HOST=gpu-n58:44883
Singularity$ ollama pull llama4:scout

To use the running Ollama server from other services similar to rollama like jupyter notebook, you'll just need to make sure you set the ollama host address as indicated previously and it should be working with you

