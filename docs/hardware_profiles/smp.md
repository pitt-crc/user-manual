---
hide:
  - toc
---

# SMP Cluster

The SMP nodes are appropriate for programs that are parallelized using the shared memory framework. These nodes are similar to your laptop but with more memory and more CPU cores. To request a particular feature (such as an Intel host CPU), add the following directive to your job script:

```bash
#SBATCH --constraint=intel
```

Multiple features can be requested by providing a comma-separated list (without intervening spaces):

```bash
#SBATCH --constraint=amd,genoa
```

<table border="1" cellpadding="1" cellspacing="1" style="width: 941px;">
	<thead>
		<tr>
			<th scope="col" style="width: 67px; border-color: rgb(170, 170, 170);">Partition</th>
			<th scope="col" style="width: 205px; border-color: rgb(170, 170, 170);">Architecture</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 116px;">--constraint</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 38px;">Nodes</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 69px;">Cores/Node</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Mem/Node</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Mem/Core</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Scratch</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 62px;">Network</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 117px;">Nodes</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td colspan="1" rowspan="3" style="vertical-align: top; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">smp</span></td>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-9374f">AMD EPYC 9374F (Genoa)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">amd, genoa</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">43</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">768 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">12 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">3.2 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-n[214-256]</td>
		</tr>
		<tr>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-7302">AMD EPYC 7302 (Rome)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">amd, rome</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">55</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">32</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">256 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB SSD</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-n[156-210]</td>
		</tr>
		<tr>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="color:#FF0000;">Retired</span>&nbsp;<s>&nbsp;<a href="https://ark.intel.com/content/www/us/en/ark/products/120483/intel-xeon-gold-6126-processor-19-25m-cache-2-60-ghz.html">Intel Xeon Gold 6126 (Skylake)</a></s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;"><s>intel, skylake</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;"><s>132</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;"><s>24</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><s>192 GB</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><s>8 GB</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><s>500 TB SSD</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;"><s>10GbE</s></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;"><s>smp-n[24-113,115-122,126-155]</s></td>
		</tr>
		<tr>
			<td colspan="1" rowspan="4" style="vertical-align: top; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">high-mem</span></td>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html">Intel Xeon Platinum 8352Y (Ice Lake)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">intel, ice_lake</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">8</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-1024-n[1-8]</td>
		</tr>
		<tr>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html">Intel Xeon Platinum 8352Y (Ice Lake)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">intel, ice_lake</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">2</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2 TB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">32 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-2048-n[0-1]</td>
		</tr>
		<tr>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/support/cpu/amd-epyc/amd-epyc-7001-series/amd-epyc-7351">AMD EPYC 7351 (Naples)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">amd, naples</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">1</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">32</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">32 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-1024-n0</td>
		</tr>
		<tr>
			<td style="width: 205px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/93801/intel-xeon-processor-e78870-v4-50m-cache-2-10-ghz/specifications.html">Intel Xeon E7-8870v4 (Broadwell)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 116px;">intel, broadwell</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 38px;">4</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 69px;">80</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">3 TB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">38 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">5 TB SSD</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 62px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 117px;">smp-3072-n[0-3]</td>
		</tr>
	</tbody>
</table>

