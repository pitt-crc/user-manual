# File Systems

The CRCD provides several distinct file spaces, each serving a different purpose:

| Path          | Purpose                          | Default Quota | Snapshots              |
|---------------|----------------------------------|---------------|------------------------|
| `/ihome`      | Home directories                 | 75 GB         | Daily, 8-day retention |
| `/vast`       | High-performance project storage | 1 TB          | Daily, 8-day retention |
| `/ix`, `/ix1` | Standard-tier project storage    | 5 TB          | 7-day retention        |

---

## `/ihome` — Home Directories

**Purpose:** Houses user home directories.

**Location:** `$HOME` or `/ihome/<primary group>/$USER`

**Underlying storage:** VAST Data all-flash

**Details:**

The root of this filesystem contains subdirectories for each user group, with individual user
directories within. Your home directory is the login directory and entry point when accessing
the CRCD ecosystem. It is also the default location for configuration files, web portal session
logs, and user-level Python and R environments.

**Quota:** 75 GB (cannot be increased). Use `crc-quota` to check utilization.

**Permissions:**

```
/ihome/<primary group>        root:<primary group>   750  (drwxr-x---)
/ihome/<primary group>/$USER  $USER:<primary group>  755  (drwxr-xr-x)
```

**Snapshots:** Daily snapshots with 8-day retention are available for file restoration.

---

## `/vast` — High-Performance Project Storage

**Purpose:** High-throughput project and working data storage for I/O-intensive workloads.

**Location:** `/vast/<primary group>`

**Underlying storage:** VAST Data all-flash

**Details:**

PIs and group members can access this folder and create their own subfolders. Use this
filesystem for data requiring high I/O throughput, such as large genomics datasets, ML
training data, or intermediate results from compute jobs.

**Quota:** 1 TB default at no charge. Additional storage can be purchased a subsidized rate of $85/TB/year. 
Use `crc-quota` to check utilization.

**Permissions:**

```
/vast/<primary group>  <group owner>:<primary group>  770  (drwxrwx---)
```

**Snapshots:** Daily snapshots with 8-day retention are available for file restoration,
consistent with the `/ihome` snapshot policy.

---

## `/ix` and `/ix1` — Standard-Tier Project Storage

**Purpose:** Enterprise storage for persistent project data.

**Location:** `/ix/<primary group>` or `/ix1/<primary group>`

**Underlying storage:** iXsystems enterprise storage, ZFS-backed with 12 TB NVMe caching

**Details:**

PIs and group members can access this folder and create their own subfolders. Use this
filesystem to stage data for compute jobs or store results that need to persist long-term.

**Quota:** 5 TB default at no charge. Additional storage can be purchased in 5 TB increments
at a subsidized rate of $65/TB/year. Use `crc-quota` to check utilization.

**Permissions:**

```
/ix/<primary group>   <group owner>:<primary group>  770  (drwxrwx---)
/ix1/<primary group>  <group owner>:<primary group>  770  (drwxrwx---)
```

**Snapshots:** `/ix` and `/ix1` are **not backed up**. 7-day snapshots are available for
accidental deletion recovery.

---

## `/bgfs` and `/zfs` — Decommissioned

These filesystems are no longer being provisioned and are actively being decommissioned.
If your group still has data on `/bgfs`, `/zfs1`, or `/zfs2` and you haven't heard from
our team about migrating it yet, please
[submit a help request](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and we will work with you to move your data to `/ix1`.

---

## Frequently Asked Questions

### A 5 TB location on `/ix` or `/ix1` was not provisioned upon my faculty account's creation. How can I request this?

Please [submit a help request](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and someone from the team will provision the space for you.

### I've hit the limit on my initial 5 TB on `/ix` or `/ix1`. How do I request more space?

Additional storage is available at a subsidized rate of $65/TB/year.
[Use this form](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=D8BjnEQtuz0_&RequestorType=Service)
to request additional space.

### How do I check how close I am to exceeding my storage quotas?

Use the `crc-quota` command:

```
[nlc60@login0 ~]$ crc-quota
User: 'nlc60'
-> ihome: 59.63 GB / 75.0 GB

Group: 'sam'
-> ix: 1.76 TB / 5.0 TB
-> vast: 0.43 TB / 1.0 TB
```

### How do I restore files that were accidentally deleted?

Snapshots are available on `/ihome`, `/vast`, `/ix`, and `/ix1`.
[Submit a help request](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
to request file restoration from a snapshot.
