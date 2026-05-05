# The Module System

CRCD uses [Lmod](https://www.tacc.utexas.edu/research-development/tacc-projects/lmod) to manage software environments. No modules are loaded by default when you log in.

## Finding Software

Search for available modules with `module spider`:

```commandline
module spider intel
```

Use regex for broader searches:

```commandline
module -r spider '.*intel.*'
```

## Loading Modules

```commandline
module load intel/2017.1.132
```

Only one version of a package can be loaded at a time. Loading a second version automatically replaces the first.

### Handling Dependencies

Some packages require prerequisite modules. Attempting to load them directly will show what dependencies are needed:

```commandline
[user@login1 ~]$ module load vasp
Lmod has detected the following error: These module(s) exist but cannot be loaded as
requested: "vasp"
 Try: "module spider vasp" to see how to load the module(s).
```

Use `module spider <package/version>` to see the required dependencies, then load them in order:

```commandline
module load intel/2017.1.132
module load intel-mpi/2017.1.132
module load vasp/5.4.1
```

## Listing Loaded Modules

```commandline
module list
```

## Unloading Modules

Unload a specific module:

```commandline
module unload package1
```

Unload all modules:

```commandline
module purge
```

!!! tip
    Always start batch scripts with `module purge` to ensure a clean environment before loading the modules your job requires.
