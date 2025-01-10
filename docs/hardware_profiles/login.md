---
title: Login Node Specifications
tags:
  - CRC
  - Hardware
  - Login Nodes
  - User Manual
---

# Login Nodes Overview
 
The Login Nodes provide access to a Linux Commandline interface on the CRCD system via Secure SHell protocol (ssh).

## Key Features

- Load balancing between login nodes to better address usage demands
- Cgroup-based management of system resources  

## Specifications

| hostname         | backend hostname         | Architecture                                                                                                                                                         | Cores/Node   | Mem      | Mem/Core   | OS Drive                   | Network   |
|------------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|----------|------------|----------------------------|-----------|
| h2p.crc.pitt.edu | login0.crc.pitt.edu      | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32           | 256 GB   | 8 GB       | 2x 480 GB NVMe (RAID 1)    | 25GbE     |
|                  | login1.crc.pitt.edu      | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32           | 256 GB   | 8 GB       | 2x 480 GB NVMe (RAID 1)    | 25GbE     |
| htc.crc.pitt.edu | login3.crc.pitt.edu      | [Intel Xeon Gold 6326 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215274/intel-xeon-gold-6326-processor-24m-cache-2-90-ghz/specifications.html) | 32           | 256 GB   | 8 GB       | 2x 480 GB NVMe (RAID 1)    | 25GbE     |
