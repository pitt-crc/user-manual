---
tags:
  - CRC
  - Hardware
  - VIZ
  - User Manual
---

# Viz Nodes

The Viz nodes provide a graphical desktop interface accessible through a web browser, allowing users to run
visualization software and GUI-based applications directly on CRCD resources. They are intended for tasks such as data
visualization, interactive analysis, and visual debugging.

For instructions on accessing the graphical Viz environment, see the
[Linux Desktop web portal](../getting-started/viz.md) documentation.

## Specifications

| Web URL                    | Backend Hostname    | GPU Type                                                                                 | GPU/Node | Host Architecture                                                                                                                                            | Cores/Node | Mem/Node | Mem/Core | Scratch    | Network |
| -------------------------- | ------------------- | ---------------------------------------------------------------------------------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | -------- | -------- | ---------- | ------- |
| <https://viz.crc.pitt.edu> | viz-n0.crc.pitt.edu | [GTX 1080 8GB](https://www.nvidia.com/en-ph/geforce/products/10series/geforce-gtx-1080/) | 2        | [Intel Xeon E5-2680v4 (Broadwell)](https://ark.intel.com/content/www/us/en/ark/products/91754/intel-xeon-processor-e52680-v4-35m-cache-2-40-ghz.html)        | 28         | 256 GB   | 9.1 GB   | 1.6 TB SSD | 10GbE   |
|                            | viz-n1.crc.pitt.edu | [RTX 2080 Ti 11GB](https://www.nvidia.com/en-us/geforce/20-series/)                      | 2        | [Intel Xeon Gold 6226 (Cascade Lake)](https://ark.intel.com/content/www/us/en/ark/products/193957/intel-xeon-gold-6226-processor-19-25m-cache-2-70-ghz.html) | 24         | 192 GB   | 8 GB     | 1.9 TB SSD | 10GbE   |

## Related

<div class="grid cards" markdown>

-   :material-monitor:{ .lg .middle } __Launch a desktop__

    ---

    Start the graphical Linux desktop in your browser — how to access the Viz nodes.

    [:octicons-arrow-right-24: Linux Desktop Web Portal](../getting-started/viz.md)

-   :material-web:{ .lg .middle } __Browser-based apps__

    ---

    Run notebooks, RStudio, and other interactive apps through Open OnDemand.

    [:octicons-arrow-right-24: Open OnDemand](../getting-started/open-ondemand.md)

-   :material-console:{ .lg .middle } __GUI on a compute node__

    ---

    Forward a graphical application from an interactive Slurm session with X11.

    [:octicons-arrow-right-24: Interactive Jobs](../slurm/interactive-jobs.md)

</div>
