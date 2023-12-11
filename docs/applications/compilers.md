# Compilers
GNU compilers are available in your path when you login. Newer GNU compilers are available as module environments.

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="nTable">
	<thead>
		<tr>
			<th>Compiler</th>
			<th>Version</th>
			<th>executable name</th>
			<th>AVX2 support</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>GNU C</td>
			<td>8.2.0*</td>
			<td>gcc</td>
			<td>Yes</td>
		</tr>
		<tr>
			<td>GNU C++</td>
			<td>8.2.0*</td>
			<td>g++</td>
			<td>Yes</td>
		</tr>
		<tr>
			<td>GNU Fortran</td>
			<td>8.2.0*</td>
			<td>gfortran</td>
			<td>Yes</td>
		</tr>
		<tr>
			<td>----</td>
			<td>----</td>
			<td>----</td>
			<td>----</td>
		</tr>
		<tr>
			<td>GNU C</td>
			<td>4.8.5</td>
			<td>gcc</td>
			<td>No</td>
		</tr>
		<tr>
			<td>GNU C++</td>
			<td>4.8.5</td>
			<td>g++</td>
			<td>No</td>
		</tr>
		<tr>
			<td>GNU Fortran</td>
			<td>4.8.5</td>
			<td>gfortran</td>
			<td>No</td>
		</tr>
	</tbody>
</table>


<script type="text/javascript" src="https://code.jquery.com/jquery-3.7.0.min.js"></script>
<script type="text/javascript" src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>

<script type="text/javascript">
    $(document).ready(function() {
        $('#nTable').DataTable({
            "paging": false,
            "bPaginate": false,
            "bLengthChange": false,
            "bFilter": true,
            "bInfo": false,
            "bAutoWidth": false,
            "searching": false,
            "ordering": false
        });
    });
</script>

See the man pages man <executable> for more information about flags.

*   GCC 8.2.0 is available through the Lmod Application Environment. See below.
*   Currently, HTC cluster does not support distributed parallel MPI jobs. Only shared memory parallel jobs are supported.

### Instruction sets

The Haswell CPUs support AVX2 instructions. The GCC 8.2.0 compiler support AVX2 with the -march=core-avx2 flag. The login nodes have the same architecture as the compute nodes.