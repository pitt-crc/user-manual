# Storage Tiers

!!! warning "No sensitive data can be stored"
    CRCD storage in the Open Science Environment is **not** approved for HIPAA-protected health information (PHI),
    personally identifiable information (PII), or other data under protected controls. If you are working 
    with protected data, the CRCD [Secure Research Environment (SRE)](../getting-started/access_sre.md)
    may meet the requirements.

CRCD provides several storage tiers spanning the research data lifecycle — from
fast flash for active computation to low-cost archive for cold data. Each tier
trades performance for cost, so most groups keep data across more than one.

This page covers what each tier is for, how much you get at no cost, and how much additional storage costs. For
the operational detail — full paths, permissions, snapshots, and checking your
usage with `crc-quota` — see [File Systems](../data-management/file-systems.md).

![CRCD storage tiers: Active (flash), Warm (hard disk), and Archive (cloud)](../_assets/img/hardware_profiles/crcd-storage-tiers.png)

## Project storage tiers

Project storage is allocated per research group (Principal Investigator). PIs and
their group members can create subfolders and stage data for compute jobs. The
lifecycle framing in the graphic above maps onto the CRCD tiers as follows:

| Tier | Lifecycle | File system | Media | Free allocation | Additional capacity |
| ---- | --------- | ----------- | ----- | --------------- | ------------------- |
| Performance | Active | `/vast` | All-flash | 1 TB per PI | $85 / TB / year |
| Standard | Warm | `/ix`, `/ix1` | Hard disk | 5 TB per PI | $30 / TB / year |
| [**Archive**&nbsp;*](https://services.pitt.edu/TDClient/33/Portal/KB/Article/444/Getting-Started-with-Archiving-Services) | Archive | Cloud | Cloud | — | $12 / TB / year |

The performance tier (`/vast`) is all-flash storage delivering high-throughput,
low-latency access over NFS. It is best for I/O-intensive and AI workloads that
read and write to the filesystem heavily. Each PI receives 1 TB at no cost.

The standard tier (`/ix`, `/ix1`) is enterprise hard-disk storage for persistent,
"warm" project data and for compute jobs with lower I/O demands. Each PI receives
5 TB at no cost, making it the default home for most project data.

The archive tier is low-cost cloud storage for cold data you need to retain but
rarely touch and is provided through a [**Pitt Digital service**](https://services.pitt.edu/TDClient/33/Portal/KB/Article/444/Getting-Started-with-Archiving-Services).

## Home directories (`/ihome`)

Separate from project storage, every user gets a personal home directory at
`/ihome/<primary group>/$USER`, created with your account. It is your default login
location and holds configuration files, logs, and user-level environments.

The quota is 75 GB per user at no cost, and it **cannot be increased** — keep
large datasets and heavy I/O jobs in project storage rather than in `$HOME`.

## Requesting storage

A [One-time Startup Allocation](../getting-started/getting-started-step1-account.md)
provisions 5 TB of Standard (`/ix`) and 1 TB of Performance (`/vast`) storage at no
cost, alongside your compute allocation. If storage wasn't set up with your account,
[submit a help ticket](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service).

To go beyond the free allocation, request additional capacity through the
[Increase Data Storage](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=D8BjnEQtuz0_&RequestorType=Service)
form:

| Request | Beyond | Cost |
| ------- | ------ | ---- |
| Performance storage | initial 1 TB | $85 / TB / year |
| Standard storage | initial 5 TB | $30 / TB / year |

Storage is billed annually per terabyte and is separate from the compute
[Service Units](../slurm/service-units.md) your jobs consume. The full list of
options is on the CRCD
[Service Request Forms](https://www.crcd.pitt.edu/service-request-forms) page.


## Related

<div class="grid cards" markdown>

-   :material-harddisk:{ .lg .middle } __Paths, quotas & snapshots__

    ---

    Full filesystem paths, per-tier quotas, snapshots, and checking usage with `crc-quota`.

    [:octicons-arrow-right-24: File Systems](../data-management/file-systems.md)

-   :material-swap-horizontal:{ .lg .middle } __Move data in and out__

    ---

    Transfer files with Globus, `rsync`/`scp`, SFTP clients, and cloud tools.

    [:octicons-arrow-right-24: File Transfer Methods](../data-management/file-transfer-methods/index.md)

-   :material-server:{ .lg .middle } __The compute clusters__

    ---

    The clusters this storage feeds, and which one fits your work.

    [:octicons-arrow-right-24: Our Clusters](index.md)

</div>
