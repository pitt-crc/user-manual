# RStudio Server on the GPU Cluster

[Open OnDemand](https://openondemand.org) gives researchers remote web access to RStudio Server with the ability to
connect to dedicated GPUs. Through this web portal, researchers can use GPU compute nodes to accelerate data analysis
and AI/ML workloads.

## Step 1. Connecting to Open OnDemand

Point your browser to the address below and authenticate with your Pitt credentials. Your username is the same one you
use for my.pitt.edu, in all lowercase. The host should be reachable for all users on Wireless-PittNet; if it isn't, try
again while connected to the [VPN](https://services.pitt.edu/TDClient/33/Portal/KB/ArticleDet?ID=3426).

- **Web hostname:** <https://ondemand.htc.crc.pitt.edu>
- **Authentication:** Pitt username (all lowercase) and password

![Open OnDemand login page](../_assets/img/ondemand/ondemand-rstudio-gpu-01.png)

## Step 2. Selecting from Interactive Apps

The **Interactive Apps** dropdown lists several installed software packages, including *RStudio Server on gpu*.

![Interactive Apps dropdown](../_assets/img/ondemand/ondemand-rstudio-gpu-02.png)

You're then taken to a form where you select the R version and GPU type, the number of CPU cores and GPU cards, the host
memory, and how long you need the resource. The *Account* field is for users who belong to multiple Slurm accounts and
want to charge the SUs to a particular account; leaving it empty charges your default allocation.

![RStudio Server submission form](../_assets/img/ondemand/ondemand-rstudio-gpu-03.png)

Pressing *Launch* submits the job to the GPU cluster, starts an RStudio Server instance on the assigned compute node,
and returns a *Connect to RStudio Server* link in the web GUI.

![Connect to RStudio Server link](../_assets/img/ondemand/ondemand-rstudio-gpu-04.png)

The job moves through three stages. *Queued* means it has been submitted to the Slurm scheduler.

![Queued status](../_assets/img/ondemand/ondemand-rstudio-gpu-05.png)

*Starting* means Slurm has allocated the requested compute resources — here, 1 node with 16 cores.

![Starting status](../_assets/img/ondemand/ondemand-rstudio-gpu-06.png)

*Running* means the RStudio Server job is executing on the indicated *Host* and will keep running until *Time Remaining*
is exhausted. Click *Connect to RStudio Server* to open the familiar RStudio interface in a new browser tab.

![Running status](../_assets/img/ondemand/ondemand-rstudio-gpu-07.png)

## Step 3. Interacting with the RStudio Server

If successful, you'll see the GUI below. The [RStudio GitHub site](https://github.com/rstudio/rstudio) links to the
[RStudio User Guide](https://docs.posit.co/ide/user/), which documents all aspects of the interface.

If you can't get an RStudio Server instance, submit a
[help ticket](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and we'll troubleshoot. A likely cause is that your account doesn't have an allocation on the requested cluster, or that
the requested resource is currently busy — see the [Appendix](#appendix-errors).

![RStudio Server GUI](../_assets/img/ondemand/ondemand-rstudio-gpu-08.png)

## Step 4. Ending your session

An RStudio Server session persists until *Time Remaining* is exhausted or you *Delete* the job. If you close the browser
window or get disconnected, the session keeps running in the background. To return to it, log in to Open OnDemand and
click *My Interactive Sessions* in the top menu to see all running or queued OnDemand jobs.

![My Interactive Sessions](../_assets/img/ondemand/ondemand-rstudio-gpu-16.png)

Clicking *Connect to RStudio Server* opens your session in a new tab:

![Reconnect to the session](../_assets/img/ondemand/ondemand-rstudio-gpu-17.png)

When you're done, terminate the session to return the compute resource to the community pool and stop the charge against
your allocation: click *Delete*, then confirm with *OK*. Save your work to the filesystem first.

![Delete the session](../_assets/img/ondemand/ondemand-rstudio-gpu-18.png)

![Confirm deletion](../_assets/img/ondemand/ondemand-rstudio-gpu-19.png)

*My Interactive Sessions* now shows the session is gone; any other OnDemand jobs you haven't deleted remain listed.

![Session removed](../_assets/img/ondemand/ondemand-rstudio-gpu-20.png)

## Appendix: Errors

Sometimes a job submitted through Open OnDemand stays in the *Queued* state for a long time. Two common causes and how
to address them are below.

### Error 1: queued due to lack of resources

In the job specification below, I request 4× A100 80 GB GPUs on the `a100_nvlink` partition. As shown on the
[GPU cluster](../hardware_profiles/gpu.md) page, this resource is limited — CRCD has only two `a100_nvlink` nodes, each
with eight of these GPUs.

![Job spec requesting 4x A100 80GB](../_assets/img/ondemand/ondemand-rstudio-gpu-error-01.png)

When I *Launch* this job, it stays *Queued* for more than five minutes, whereas my jobs typically reach *Running* within
a couple of minutes.

![Job stuck in Queued](../_assets/img/ondemand/ondemand-rstudio-gpu-error-02.png)

The job panel doesn't show the Slurm detail needed for troubleshooting, so open an SSH terminal to the cluster: click
the *Clusters* menu and select one of the *Shell Access* options.

![Clusters shell access](../_assets/img/ondemand/ondemand-rstudio-gpu-error-03.png)

In the terminal that opens, run:

```
squeue -M gpu -u $USER
```

![squeue output showing pending job](../_assets/img/ondemand/ondemand-rstudio-gpu-error-04.png)

#### Possible solution

The `squeue` output shows the job in the *PD* (pending) state with reason *(Resources)*, meaning Slurm can't find
resources matching the request; the job stays pending until other jobs using those resources finish.

One fix is to target a different, more available resource. Below, I changed the GPU type from A100 80 GB to A100 40 GB.

![Job spec changed to A100 40GB](../_assets/img/ondemand/ondemand-rstudio-gpu-error-05.png)

Slurm then found the resources and the job entered *Running*, allocating 4 GPUs on node `gpu-n28.crc.pitt.edu` with 64
CPU cores.

![Job now running](../_assets/img/ondemand/ondemand-rstudio-gpu-error-06.png)

Running `squeue` again shows output matching the job panel.

![squeue output matching the panel](../_assets/img/ondemand/ondemand-rstudio-gpu-error-07.png)

### Error 2: queued due to no allocation

This error occurs when you have no allocation on a cluster, or the allocation has expired. To reproduce it for this
guide, I zeroed out my GPU-cluster allocation (visible in the terminal below) and requested one L40S GPU.

![No allocation on GPU](../_assets/img/ondemand/ondemand-rstudio-gpu-error-10.png)

The launched job stays *Queued* for several minutes.

![Job stuck in Queued (no allocation)](../_assets/img/ondemand/ondemand-rstudio-gpu-error-11.png)

To debug, open a terminal from the *Clusters* menu via *Shell Access*.

![Clusters shell access](../_assets/img/ondemand/ondemand-rstudio-gpu-error-12.png)

Run `squeue -M all -u $USER` to show your jobs on every cluster. The pending reason is *AssocGrpBillingMinutes*, meaning
the job has no SUs to draw from — either the allocation expired or you have none on the requested cluster (here, the
latter).

![squeue showing AssocGrpBillingMinutes](../_assets/img/ondemand/ondemand-rstudio-gpu-error-13.png)

#### Possible solution

You need an allocation on the requested cluster. Submit a
[Service Request Form](https://crc.pitt.edu/service-request-forms), choosing the *One-Time Startup Allocation* if this
is your first request for computing time, or the *Annual Project Allocation* for a renewal.
