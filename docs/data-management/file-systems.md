# File Systems

The CRCD provides several distinct file spaces, each serving a different purpose:

## `/ihome` (Home Directories)

Purpose: Houses user home directories.

Location: `$HOME` or `/ihome/<primary group>/$USER`

Details:

The root of this filesystem contains subdirectories for each user group, with individual user directories within.

These individual user directories serves as the login directory and is the entry point when a user logs into the CRCD 
ecosystem.

Your home directory is the default location for various configuration files, session logs for jupyterhub and the other
web portals, and user-level environments in python, R, etc.

Quota: 75 GB (cannot be increased). Users can check their quota utilization using the `crc-quota` command.

Permissions:

    `/ihome/<primary group>` is owned by root:<primary group> with permissions 750 (drwxr-x---).

    `/ihome/<primary group>/$USER` is owned by $USER:<primary group> with permissions 755 (drwxr-xr-x).

Snapshots: User home directories are backed up daily.

## `/ix` and `/ix1` (Project Data Storage)

Purpose: Enterprise storage locations for persistent file storage.

Location: `/ix/<primary group>` or `/ix1/<primary group>`

Details:

PIs and group members can access this folder and create their own subfolders. Use this file system to stage data for
use in your compute jobs.

Quota: 5 TB (default allocation, no charge). Users can check their quota utilization using the `crc-quota` command.

Additional storage can be purchased in 5 TB increments at a subsidized rate of $85 per TB per year.

Permissions:

    /ix/<primary group> or /ix1/<primary group> is owned by <group owner>:<primary group> with permissions 770 (drwxrwx---).

Snapshots: /ix or /ix1 folders are NOT backed up. 7-day snapshots are available for file restoration.

## `/bgfs` (BeeGFS File System)

Purpose: Persistent file storage (no longer being provisioned).

Details:

Groups are actively being migrated off /bgfs in favor of /ix1

Quota: Varies by group. Users can check their quota utilization using the `crc-quota` command.

Snapshots: **Not available.**

## `/zfs1` and `/zfs2` (Archival Storage)

Purpose: Archival storage with a total capacity of 1 PB (no longer being provisioned).

Details:

Groups are actively being migrated off /zfs1 and /zfs2 in favor of /ix1

Quota: Varies by group. Users can check their quota utilization using the `crc-quota` command.

Snapshots: 7-day snapshots are available for file restoration.

## Frequently Asked Questions

### A 5TB location on /ix or /ix1 was not provisioned to me upon my faculty account's creation. How can I request this?
Please [submit the help request form](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
and someone from the team will provision the space for you.

### I've hit the limit on my initial 5TB provision on /ix or /ix1, how do I request more space?
5 TB increments of extra storage are available for purchase at a subsidized rate of $85 per TB per year.
[Use this form](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=D8BjnEQtuz0_&RequestorType=Service)
to request additional storage space beyond 5 TB.

### How do I check how close I am to exceeding my storage quotas? 
You can check your usage and quotas across these filesystems with the wrapper script command `crc-quota`:

```commandline
[nlc60@login0b ~] : crc-quota
User: 'nlc60'
-> ihome: 59.63 GB / 75.0 GB

Group: 'sam'
-> zfs1: 1.76 TB / 5.0 TB
-> beegfs: 14.68 TB / 80.0 TB
```

### How do I restore some files that were accidentally deleted?
Depending on the file system, snapshots are available to restore accidentally deleted files
[Submit the Help Request form to request file restoration](https://services.pitt.edu/TDClient/33/Portal/Requests/TicketRequests/NewForm?ID=yXkHi62rHa8_&RequestorType=Service)
