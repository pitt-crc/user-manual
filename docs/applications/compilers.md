# Software Development
## Build Systems
Make and CMake are available on your path when you login. Newer CMake versions and Bazel can be loaded through the module environment.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="buildTable">
	<thead>
		<tr>
			<th>Build System</th>
			<th>Latest Module</th>
			<th>Executable name</th>
			<th>Default Version</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>CMake </td>
			<td> cmake/3.31.8-fqskamn</td>
			<td>cmake</td>
			<td>3.26.5</td>
		</tr>
		<tr>
			<td>Bazel</td>
			<td>bazel/7.0.2</td>
			<td>bazel</td>
			<td>Not available</td>
		</tr>
	</tbody>
</table>




## Compilers and Interpreters
GNU compilers and Python interpreter are available in your path when you login. Newer versions of GNU compilers are available as module environments.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="compTable">
	<thead>
		<tr>
			<th>Name</th>
			<th>Latest Module</th>
			<th>Executable name</th>
			<th>Default Version</th>
			<th>Executing Modus</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>GNU C</td>
			<td>gcc/15.1.0</td>
			<td>gcc</td>
			<td>11.5.0</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>GNU C++</td>
			<td>gcc/15.1.0</td>
			<td>g++</td>
			<td>11.5.0</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>GNU Fortran</td>
			<td>gcc/15.1.0</td>
			<td>gfortran</td>
			<td>11.5.0</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>Intel C</td>
			<td>intel-oneapi-compilers/2025.2.0</td>
			<td>icx</td>
			<td>Not available</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>Intel C++</td>
			<td>intel-oneapi-compilers/2025.2.0</td>
			<td>icpx</td>
			<td>11.5.0</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>Intel Fortran</td>
			<td>intel-oneapi-compilers/2025.2.0</td>
			<td>ifx</td>
			<td>Not available</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>OpenMPI C</td>
			<td> openmpi/5.0.6</td>
			<td>mpicc</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>OpenMPI C++</td>
			<td> openmpi/5.0.6</td>
			<td>mpic++</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>OpenMPI Fortran</td>
			<td> openmpi/5.0.6</td>
			<td>mpifort</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>Intel-MPI C</td>
			<td> intel-oneapi-mpi/2021.11.0</td>
			<td>mpiicc</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>Intel-MPI C++ </td>
			<td> intel-oneapi-mpi/2021.11.0</td>
			<td>mpiicpx</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>Intel MPI Fortran</td>
			<td> intel-oneapi-mpi/2021.11.0</td>
			<td>mpiifort</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
				<tr>
			<td>MPICH C</td>
			<td> mpich/4.1.2</td>
			<td>mpicc</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>MPICH C++ </td>
			<td> mpich/4.1.2</td>
			<td>mpic++</td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>MPICH Fortran</td>
			<td>   mpich/4.1.2</td>
			<td>mpifort </td>
			<td>Not available</td>
			<td>Compiled Wrapper</td>
		</tr>
		<tr>
			<td>CUDA C</td>
			<td>   cuda/12.9.0</td>
			<td>nvcc </td>
			<td>Not available</td>
			<td>Compiled for GPU</td>
		</tr>
		<tr>
			<td>Rust</td>
			<td> rust/1.89.0</td>
			<td>rustc</td>
			<td>1.88.0</td>
			<td>Compiled</td>
		</tr>
		<tr>
			<td>Go</td>
			<td> go/1.24.5</td>
			<td>go</td>
			<td>1.22.9</td>
			<td>Compiled</td>
		</tr>
			<tr>
			<td>Java</td>
			<td>   java/24.0.1</td>
			<td>javac </td>
			<td>17.0.14</td>
			<td>Compiled Interpreted</td>
		</tr>
		<tr>
			<td>Perl</td>
			<td> perl/5.38.0</td>
			<td>perl</td>
			<td>5.32</td>
			<td>Interpreted</td>
		</tr>
			<tr>
			<td>Python</td>
			<td> python/3.13.5</td>
			<td>python</td>
			<td>3.9.21</td>
			<td>Interpreted</td>
		</tr>
	</tbody>
</table>

## Debugger and Profilers
<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="buildTable">
	<thead>
		<tr>
			<th>Name</th>
			<th>Latest Module</th>
			<th>Executable name</th>
			<th>Default Version</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>GNU Debugger</td>
			<td> Not available</td>
			<td>gdb</td>
			<td>14.2</td>
		</tr>
		<tr>
			<td>CUDA GDB</td>
			<td>cuda/12.9.0</td>
			<td>cuda-gdb</td>
			<td>Not available</td>
		</tr>
				<tr>
			<td>Valgrind</td>
			<td>valgrind/3.23.0</td>
			<td>valgrind</td>
			<td>3.23.0</td>
		</tr>
					<tr>
			<td>Intel VTune Profiler</td>
			<td>intel-oneapi-vtune/2025.4.0</td>
			<td>vtune</td>
			<td>Not available</td>
		</tr>
		<tr>
			<td>NVIDIA Nsight Systems </td>
			<td>nvidia-nsight-systems/2024.6.1</td>
			<td>nsys, ncu</td>
			<td>Not available</td>
		</tr>				
	</tbody>
</table>


See the man pages man <executable> for more information about flags.

*   Any compiler or interpreter versions different than the default are available through the Lmod Application Environment. 
*   Currently, HTC cluster does not support distributed parallel MPI jobs. Only shared memory parallel jobs are supported.

### Instruction sets

The Haswell CPUs support AVX2 instructions. The GCC 8.2.0 compiler support AVX2 with the -march=core-avx2 flag. The login nodes have the same architecture as the compute nodes.
