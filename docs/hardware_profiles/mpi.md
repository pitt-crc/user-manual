---
title: MPI Cluster Specifications
tags:
  - CRC
  - Hardware
  - MPI Computing
  - User Manual
---

# MPI Cluster Overview

The MPI cluster enables jobs with tightly coupled parallel codes using Message Passing Interface APIs for distributing 
computation across multiple nodes, each with its own memory space.

## Key Features

- Infiniband and Omni-Path networking 
- Minimum of 2 Nodes per Job

## Specifications

| Partition      | Architecture                                                                                                                                                         | Nodes | Cores/Node | Mem/Node | Mem/Core | Scratch     | Network       | Nodes         |
|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|------------|----------|----------|-------------|---------------|---------------|
| `mpi`          | [Intel Xeon Gold 6342 (Ice Lake)](https://www.intel.com/content/www/us/en/products/sku/215276/intel-xeon-gold-6342-processor-36m-cache-2-80-ghz/specifications.html) | 136   | 48         | 512 GB   | 10.6 GB  | 1.6 TB NVMe | HDR200; 10GbE | mpi-n[0-135]  |
| `opa-high-mem` | [Intel Xeon Gold 6132 (Skylake)](https://ark.intel.com/content/www/us/en/ark/products/123541/intel-xeon-gold-6132-processor-19-25m-cache-2-60-ghz.html)              | 36    | 28         | 192 GB   | 6.8 GB   | 500 TB SSD  | OPA; 10GbE    | opa-n[96-131] |
