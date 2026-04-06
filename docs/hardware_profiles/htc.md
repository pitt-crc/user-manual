---
title: HTC Cluster Specifications
tags:
  - CRC
  - Hardware
  - HTC Computing
  - User Manual
---

# HTC Cluster Overview

The HTC Cluster is designed to handle data-intensive health sciences workflows (genomics, neuroimaging, etc.) 
processing that can run on a single node.

## Key Features

- Dedicated Open OnDemand web portal instance

## Specifications

| Partition | Nodes | GPU | VRAM | GPU/Node | --constraint   | CPU                       | Cores/Node | Mem/Node | Scratch   | Network | Node Names                   |
|-----------|-------|-----|------|----------|----------------|---------------------------|------------|----------|-----------|---------|------------------------------|
| htc       | 72    | N/A | N/A  | N/A      | intel,ice_lake | Intel Xeon Platinum 8352Y | 81         | 768 GB   | 3 TB NVMe | 10GbE   | htc-1024-n[0-3],htc-n[24-91] |