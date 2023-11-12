# Cromwell/WDL and ENCODE Pipelines

## Introduction

The Workflow Description Language, or WDL (pronounced “widdle”), is a technical language developed at the Broad 
Institute that allows workflow developers to describe pipelines in a hardware-agnostic way — separating logic from 
the metal on which it runs. A WDL workflow is composed of a sequence of Tasks, each having its own inputs, outputs,
runtime environment, and commands. The runtime block outlines the shape of the virtual machine image in which the 
list of commands will run, producing outputs from inputs. Note that each WDL Task prescribes a custom runtime 
environment, which lends to very lean utilization of computational resources.

Cromwell is a workflow execution engine that can run WDL workflows, locally or in the cloud.

Caper (Cromwell Assisted Pipeline ExecutoR) is a wrapper Python package for Cromwell. Caper supports multiple platforms.

```commandline
# run the following command on HTC login node to generate caper configuration files under ~/.caper
module load caper/2.2.2
caper init slurm
```

This will create a default Caper configuration file ~/.caper/default.conf, which have only required parameters for 
slurm, the platform for the HTC cluster. This will also install Cromwell/Womtool JARs on ~/.caper. 
Downloading those files can take several minutes. Once they are installed, Caper can completely work offline with 
local data files.
