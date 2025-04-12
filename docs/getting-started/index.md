---
hide:
  - toc
---

# Big Picture Overview

The University of Pittsburgh provides its research community access to high performance computing and data storage
resources. These systems are maintained and supported through the Center for Research Computing and Data (CRCD) and Pitt IT.
To get started, you will need a CRCD account, with which you will use to login to Access Portals to interact with
the CRCD Ecosystem.

A schematic of the process is depicted below.

![GETTING-STARTED-MAP](../_assets/img/getting-started/getting-started-map.png)

<ins>**Who has Access?**</ins>

The [**CRCD-P3: Open Science Research Computing and Data Environment Usage Policy**](https://crc.pitt.edu/sites/default/files/assets/CRC%20P3%20Open%20Science%20Research%20Computing%20and%20Data%20Environment%20Usage%20Policy.pdf) defines
the scope of access. Briefly, these computing and data resources are available to all Pitt faculty, instructors, Emeritus faculty or center 
directors. These PIs, in turn, can request access for their students, post doctoral fellows, and staff. Access for Pitt alumni and external 
collaborators are possible through the [**Sponsored Account**](sponsored_account.md) mechanism.

<ins>**Definitions**</ins>

* **Client** -- this is your computer or internet-connected device
* **Access Portal** -- one of several remote servers used to submit jobs to the high performance computing clusters or
  to perform
  data management operations
* **CRCD Ecosystem** -- the total footprint of the CRCD infrastructure, including high performance computing
  clusters, data storage systems, networking equipment, and software

<ins>**Available Resources**</ins>

| Cluster Acronym                    | Expansion                 | Description of Use Cases                                                                                                                                          |
|------------------------------------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [MPI](../hardware_profiles/mpi.md) | Message Passing Interface | For tightly coupled parallel codes that use the Message Passing Interface APIs for distributing computation across multiple nodes, each with its own memory space |
| [HTC](../hardware_profiles/htc.md) | High Throughput Computing | For genomics and other health sciences-related workflows that can run on a single node                                                                            |
| [SMP](../hardware_profiles/smp.md) | Shared Memory Processing  | For jobs that can run on a single node where the CPU cores share a common memory space                                                                            |
| [GPU](../hardware_profiles/gpu.md) | Graphics Processing Unit  | For AI/ML applications and physics-based simulation codes that had been written to take advantage of accelerated computing on GPU cores                           |
