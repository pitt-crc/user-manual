# Compilers

GNU compilers are available in your path at login. Newer versions and additional compilers are available through the module system.

## Available Compilers

| Compiler | Version | Executable | AVX2 Support | How to Access |
|----------|---------|-----------|--------------|---------------|
| GNU C | 8.2.0 | `gcc` | Yes | `module load gcc/8.2.0` |
| GNU C++ | 8.2.0 | `g++` | Yes | `module load gcc/8.2.0` |
| GNU Fortran | 8.2.0 | `gfortran` | Yes | `module load gcc/8.2.0` |
| GNU C | 4.8.5 | `gcc` | No | Available by default (system) |
| GNU C++ | 4.8.5 | `g++` | No | Available by default (system) |
| GNU Fortran | 4.8.5 | `gfortran` | No | Available by default (system) |

Use `man <executable>` for detailed information about compiler flags.

## Notes

- GCC 8.2.0 and newer are available through the Lmod module system
- The compute nodes support AVX2 instructions; use `-march=core-avx2` with GCC 8.2.0+ to enable them
- Login nodes have the same architecture as compute nodes, so code compiled on login nodes will run on compute nodes
- The HTC cluster does not support distributed parallel MPI jobs; only shared memory (OpenMP) parallelism is supported on HTC
