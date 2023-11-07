# Scavenger Partitions

A note on Scavenger partition deprecation:
------------------------------------------

In our recent cluster-wide maintenance sessions, we have taken steps to move away from our preemptible-job-based "scavenger" partitions.

For example, users may have noticed that:

- The GPU cluster has had it's scavenger partition removed.

- We no longer add new nodes to the scavenger partitions of other clusters when the hardware is retired.

Our team has made these decisions in the context of a larger conversation around our Allocation Proposal process, and the SLURM workload manager configuration used on the clusters:

**There are abundant compute resources are available upon request**

A "Standard Allocation" of 25,000 service units per year is available to all faculty accounts, and is usable on any cluster to benchmark workflows and prepare a larger request for resources. The proposal process is sufficient for nearly all use cases we've encountered previously, and gives us a way to document the changing demands of our diverse user base over time. We will be increasing the standard allocation amount as well to help reduce any inconvenience caused by removing scavenger.

**It simplifies our SLURM configuration related to job priority**

As they are configured, the scavenger partitions have been found to create situations where jobs charging an account's service units are **not** preempting scavenger jobs due to how their priority factor is established. There are other ways within SLURM to manage priority and avoid this, so our intention is to simplify the configuration and ultimately provide more transparency to our users about how priority is computed.

Scavenger Partition Overview
----------------------------

To enable better utilization of available compute resources, each cluster has a "scavenger" partition defined.

The scavenger partition allows groups to use up to 90% of the resources on the cluster, do not incurr a service unit cost, and do not enforce any time limits. With this, the jobs on this partition are **preemptible** and will only run if there are idle resources. They can also be canceled by Slurm as jobs enter the paid partitions.

Common Scavenger partition use cases
------------------------------------

The scavenger partitions are great when you need to run many short jobs or when you have a long-running, restartable job.

In either case, jobs should be able to save partial output or report any failures in their outputs in the event of cancellation.

### Many short jobs

When submitting many short jobs without requeue (the default), you control when and where to resubmit the preempted jobs.  
The main downside to this is that you'll need to write a script to requeue preempted jobs or adjust the partition and resubmit.

If you choose to requeue, you can start these jobs and forget about them, but they may continue to get preempted over and over again.

Note: There is a cap 1000 jobs per user per cluster for the scavenger partition because the queue becomes unstable when users launch short jobs in quick succession.

### Long restartable jobs

In that your job could be cancelled at any time, longer jobs should create intermediate outputs, and be set up to detect files to restart from when requeuing, making sure that input files in the correct places. Submit your job into the scavenger partition with the ```--requeue``` option.

Submitting to the scavenger partitions
--------------------------------------

You can request the scavenger partition with #SBATCH --partition=scavenger. This will queue your job with a QOS that sets it at a particularly low priority.

Note: For the gpu scavenger partition you must specify which type of node as well:

```#SBATCH --constraint=<partition>```, where partition can be one of the following: gtx1080, titanx, k40, v100

Note: It is currently possible to submit a job to the paid partitions that, taking fairshare and other job priority factors into account, can **have a lower priority than a scavenger job and not preempt them**. This should be a rare occurence due to scavenger jobs being such a low priority as it is and may be prevented by submitting smaller batches of jobs at a time.

We are working on documenting this problem, so if you notice it happening, please [submit a ticket](https://crc.pitt.edu/tickets) with the output of:

**```sprio -M gpu -S "-y" --format %.15i,%.9r,%.10Y,%.10u```**

to show the sorted priority of jobs on queued on GPU, and then the output of

**```squeue -M gpu```**

to show your job pending, the reason, and the job running on scavenger that has not been preempted. 

Job Cancellation and Requeueing
-------------------------------

If preempted by a paid partition job, the default behavior is for the job to be cancelled. 

There is an option to place the job back in the queue, however, with an SBATCH argument:

```#SBATCH --requeue```