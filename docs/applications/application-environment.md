# Application Environment

The CRC utilizes [LMOD](https://www.tacc.utexas.edu/research-development/tacc-projects/lmod) to provide optimized 
builds of commonly used software.

Applications are available to users through the LMOD modular environment commands.

There are no default modules loaded when users log in.

## Searching for and Loading Installed packages

To find the module you are looking for, simply use the spider command. For example, if you are searching for intel compilers:

```commandline
$ module spider intel
```

In this case, there are&nbsp;two versions of intel compilers. Be aware which version has the compatibility with your 
code and load the one you need accordingly.&nbsp;There is also another module with “intel” in the module name. 
You can find all the possibilities of having the word “intel” in the name of the modules by:

```commandline
module -r spider '.*intel.*'
```

There may also be MPI versions of certain software, for example:

`intel-mpi: intel-mpi/2017.3.196`

For safety, LMOD allows only one version of the package to be available. So if a user does:

```commandline
$ module load intel/2011.12.361
$ module load intel/2017.1.132
```

The module loaded second will take the place of the module loaded first.

Some software will require that you load other dependency modules first. 
This should be clear from the message you get when you attempt to load, or from the details you search for a module.

In the example below, the intel and intel-mpi modules are loaded&nbsp;as prerequisites to load the vasp package. 
The executables, such as `vasp_std`,`vasp_gam` and `vasp_ncl` are now in my PATH.

```commandline
[shs159@login1 ~]$ module load vasp
LMOD has detected the following error: These module(s) exist but cannot be loaded as
requested: "vasp"

 Try: "module spider vasp" to see how to load the module(s).

[shs159@login1 ~]$ module load intel/2017.1.132
[shs159@login1 ~]$ module load intel-mpi/2017.1.132
[shs159@login1 ~]$ module load vasp
vasp vasp/5.4.1 vasp-vtst vasp-vtst/5.4.1
[shs159@login1 ~]$ module load vasp/5.4.1
[shs159@login1 ~]$ vasp_
vasp_gam vasp_ncl vasp_std
```

## See which Modules are Currently Loaded

You can check which modules are “loaded” in your environment by using the command&nbsp;module list

```commandline
[shs159@login1 ~]$ module list

Currently Loaded Modules:
 1) intel/2017.1.132 2) intel-mpi/2017.1.132 3) vasp/5.4.1
```

## Unloading Modules

To unload a module:

```commandline
$ module unload package1 package2 ...
```

To unload all modules:

```commandline
$ module purge
```

