---
title: VIZ Node Specifications
tags:
  - CRC
  - Hardware
  - VIZ
  - User Manual
---

# VIZ Overview

The VIZ Login Nodes enable access to an in-browser Linux Desktop environment on the CRCD system.

## Key Features

- Load balancing between login nodes to better address usage demands
- Cgroup-based management of system resources  

## Specifications

| **Web URL**                                         | **backend hostname** | **GPU Type**                                                                             | **# GPUs** | **Host Architecture**                                                                                                                                        | **Cores** | **Mem** | **Mem/Core** | **Scratch** | **Network** |
|:----------------------------------------------------|----------------------|------------------------------------------------------------------------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------|---------|--------------|-------------|-------------|
| [https://viz.crc.pitt.edu](https://viz.crc.pitt.edu)| viz-n0.crc.pitt.edu  | [GTX 1080 8GB](https://www.nvidia.com/en-ph/geforce/products/10series/geforce-gtx-1080/) | 2          | [Intel Xeon E5-2680v4 (Broadwell)](https://ark.intel.com/content/www/us/en/ark/products/91754/intel-xeon-processor-e52680-v4-35m-cache-2-40-ghz.html)        | 28        | 256 GB  | 9.1 GB       | 1.6 TB SSD  | 10GbE       |
|                                                     | viz-n1.crc.pitt.edu  | [RTX 2080 Ti 11GB](https://www.nvidia.com/en-us/geforce/20-series/)                      | 2          | [Intel Xeon Gold 6226 (Cascade Lake)](https://ark.intel.com/content/www/us/en/ark/products/193957/intel-xeon-gold-6226-processor-19-25m-cache-2-70-ghz.html) | 24        | 192 GB  | 8 GB         | 1.9 TB SSD  | 10GbE       |
