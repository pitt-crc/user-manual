---
hide:
  - toc
---

# Step 2: Login to Access Portals

Once you have established a VPN connection to PittNet, you can access CRCD resources through several portals:

- [SSH terminal](../terminal.md) - Command-line access
- [Linux Desktop (VIZ)](../viz.md) - Remote graphical desktop
- [Open OnDemand](../open-ondemand.md) - Web-based cluster management
- [JupyterHub](../jupyter-hub.md) - Interactive notebooks
- [JupyterHub on Teach](../jupyter-teach.md) - Notebooks for coursework

![GETTING-STARTED-MAP](../../_assets/img/getting-started/getting-started-step-2.png)

## Login Node Usage Policy

Login nodes are a shared resource for lightweight interactive work: editing code, submitting jobs, checking status, and managing files.

!!! warning "Do not run compute-intensive work on login nodes"
    - Resource-intensive processes may be terminated without notice
    - Use [interactive jobs](../../slurm/interactive-jobs.md) or [batch jobs](../../slurm/batch-jobs.md) for computational work
    - **Repeated violations may result in revoked cluster access**
