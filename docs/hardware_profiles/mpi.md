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

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint | CPU                  | Cores/Node | Mem/Node | Scratch   | Network   | Node Names                                                                                                                   |
|-----------|-------|-----|------|----------|--------------|----------------------|------------|----------|-----------|-----------|------------------------------------------------------------------------------------------------------------------------------|
| mpi       | 136   | N/A | N/A  | N/A      | hdr          | Intel Xeon Gold 6342 | 48         | 512 GB   | 2 TB NVMe | HDR200 IB | mpi-n[0-1,10,100-109,11,110-119,12,120-129,13,130-135,14-19,2,20-29,3,30-39,4,40-49,5,50-59,6,60-69,7,70-79,8,80-89,9,90-99] |
| ndr       | 18    | N/A | N/A  | N/A      | ndr          | Amd Epyc 9575F       | 128        | 1 TB     | 3 TB NVMe | HDR200 IB | mpi-n[136-153]                                                                                                               |