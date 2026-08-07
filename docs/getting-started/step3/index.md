---
hide:
  - toc
---

# Step 3- Interacting with the CRCD Ecosystem

!!! abstract "In this step"
    Upload files, find software, request resources, and run jobs

Now that you have successfully logged in an access portal, work through these in order to get jobs running:

<div class="grid cards" markdown>
-   :material-upload:{ .lg .middle } __1. Upload files__

    ---

    Move your data and code onto the cluster from your computer or cloud
    storage.

    [:octicons-arrow-right-24: File transfer](../../data-management/file-transfer-methods/index.md)

-   :material-package-variant:{ .lg .middle } __2. Discover software__

    ---

    Find the applications and libraries you need and load them with the module
    system.

    [:octicons-arrow-right-24: Discovering software](getting-started-step3-software.md)

-   :material-server:{ .lg .middle } __3. Request resources__

    ---

    Ask the scheduler for the cores, memory, and time your work needs — as a
    batch job or an interactive session.

    [:octicons-arrow-right-24: Requesting resources](getting-started-step3-resources.md)

-   :material-clipboard-check:{ .lg .middle } __4. Manage jobs__

    ---

    Monitor, inspect, and control your jobs while they run and after they finish.

    [:octicons-arrow-right-24: Managing jobs](getting-started-step3-manage-jobs.md)

</div>

---

A schematic of this part of the process is highlighted below.

![GETTING-STARTED-MAP](../../_assets/img/getting-started/getting-started-step-3.png)

<ins>**Definitions**</ins>

*   **back-end** -- the dedicated computing nodes, storage arrays, networking, and supporting management 
infrastructure are often referred to collectively as the back-end. Access to the back-end are restriced based on access policy.
*   **front-end** -- these are the access portals, the gateway to the back-end. The 
[**Slurm workload manager**](https://slurm.schedmd.com/documentation.html) controls access to the back-end computing nodes.
