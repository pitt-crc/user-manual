# Working with Software

CRCD provides a wide range of pre-installed software accessible through the [Lmod module system](application-environment.md). This section covers how to find, load, and use software on the clusters.

## Quick Start

```bash
# Search for available software
module spider <software_name>

# Load a module
module load <module_name>

# List currently loaded modules
module list

# Unload all modules
module purge
```

## Software Categories

### System Software and Tools

- [Software List](software-list.md) - Complete list of available software
- [The Module System](application-environment.md) - How Lmod manages software environments
- [Basic Module Commands](basic-commands.md) - Essential module commands
- [CRC Wrapper Tools](crc-wrappers.md) - Convenience tools for common tasks
- [Compilers](compilers.md) - Available compilers (GCC, Intel, NVIDIA)

### Languages and Frameworks

- [Python](python.md) - Python environments, conda, and virtual environments
- [R and RStudio](r+rstudio.md) - R programming and RStudio Server
- [RStudio on GPU Nodes](ondemand-rstudio-gpu.md) - GPU-accelerated R workflows
- [CUDA-Q](cuda-q.md) - Quantum computing framework

### Containers

- [Singularity](singularity.md) - Running containers on the cluster

### Licensed Software

Commercial and restricted-license software available on the clusters:

- [ANSYS](licensed-software/ansys.md) - Engineering simulation
- [COMSOL](licensed-software/comsol.md) - Multiphysics simulation
- [Lumerical](licensed-software/lumerical.md) - Photonics simulation
- [Mathematica](licensed-software/mathematica.md) - Computational mathematics
- [VASP](licensed-software/vasp.md) - Materials science simulations
- [MATLAB](licensed-software/matlab/matlab.md) - Numerical computing

!!! tip
    Use `module spider <name>` to search for software. If you need software that is not currently installed, submit a request through the [CRCD help portal](https://services.pitt.edu/TDClient/33/Portal/Requests/ServiceDet?ID=1511).
