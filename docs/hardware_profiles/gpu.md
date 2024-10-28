<table border="1" cellpadding="1" cellspacing="1">
	<thead>
		<tr>
			<th scope="col" style="width: 69px; border-color: rgb(170, 170, 170);">Partition</th>
			<th scope="col" style="width: 33px; border-color: rgb(170, 170, 170);">Nodes</th>
			<th scope="col" style="width: 105px; border-color: rgb(170, 170, 170);">GPU Type</th>
			<th scope="col" style="width: 69px; border-color: rgb(170, 170, 170);">GPU/Node</th>
			<th scope="col" style="width: 87px; white-space: nowrap; border-color: rgb(170, 170, 170);">--constraint</th>
			<th scope="col" style="white-space: nowrap; border-color: rgb(170, 170, 170);">Host Architecture</th>
			<th scope="col" style="width: 83px; border-color: rgb(170, 170, 170);">Core/Node</th>
			<th scope="col" style="width: 83px; border-color: rgb(170, 170, 170);">Max Core/GPU</th>
			<th scope="col" style="width: 119px; border-color: rgb(170, 170, 170);">Mem/Node</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Mem/Core</th>
			<th scope="col" style="width: 79px; border-color: rgb(170, 170, 170);">Scratch</th>
			<th scope="col" style="width: 130px; border-color: rgb(170, 170, 170);">Network</th>
			<th scope="col" style="width: 130px; border-color: rgb(170, 170, 170);">Nodes</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td colspan="1" style="width: 69px; vertical-align: baseline; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">l40s</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">20</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/l40s/">L40S&nbsp;48GB</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">l40s,48g,intel</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/232383/intel-xeon-platinum-8462y-processor-60m-cache-2-80-ghz/specifications.html">Intel Xeon Platinum 8462Y+</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">512 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">7 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[55-74]</td>
		</tr>
		<tr>
			<td colspan="1" rowspan="2" style="vertical-align: top; width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/a100/">A100 40GB PCIe</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100,40g,amd</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-7742">AMD EPYC 7742 (Rome)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">512 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">HDR200; 10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[35-44]</td>
		</tr>
		<tr>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/a100/">A100 40GB PCIe</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100,40g,intel</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/199354/intel-xeon-gold-5220r-processor-35-75m-cache-2-20-ghz/specifications.html">Intel Xeon Gold 5220R (Cascade Lake)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">48</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">12</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">384 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[33-34]</td>
		</tr>
		<tr>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100_multi</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/a100/">A100 40GB PCIe</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100,40g,amd</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-7742">AMD EPYC 7742 (Rome)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">512 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">HDR200; 10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[45-54]</td>
		</tr>
		<tr>
			<td colspan="1" rowspan="2" style="width: 69px; vertical-align: baseline; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100_nvlink</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/a100/">A100 80GB SXM</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100,80g,amd</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-7742">AMD EPYC 7742 (Rome)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">128</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">HDR200; 10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[31-32]</td>
		</tr>
		<tr>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">3</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-us/data-center/a100/">A100 40GB SXM</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">a100,40g,amd</span></td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-7742">AMD EPYC 7742 (Rome)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">128</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">12 TB NVMe</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">HDR200; 10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[28-30]</td>
		</tr>
		<tr>
			<td colspan="1" style="width: 69px; vertical-align: baseline; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">gtx1080</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">9</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-gb/geforce/graphics-cards/geforce-gtx-1080-ti/specifications/">GTX 1080 Ti&nbsp;11GB</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">&nbsp;</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://ark.intel.com/content/www/us/en/ark/products/123551/intel-xeon-silver-4112-processor-8-25m-cache-2-60-ghz.html">Intel Xeon Silver 4112 (Skylake)</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">2</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">96 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">12 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">480 GB SSD</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">gpu-n[17-25]</td>
		</tr>
		<tr>
			<td colspan="1" style="width: 69px; vertical-align: baseline; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">power9</span></td>
			<td style="width: 33px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.nvidia.com/en-gb/data-center/tesla-v100/">V100 32GB SXM</a></td>
			<td style="width: 69px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="width: 87px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">&nbsp;</td>
			<td style="width: 105px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.ibm.com/downloads/cas/6PRDKRJ0">IBM Power System AC922</a></td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">128 threads</td>
			<td style="width: 83px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16</td>
			<td style="width: 119px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">512 GB</td>
			<td style="width: 54px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4 GB</td>
			<td style="width: 79px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">1 TB SSD</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">HDR100; 10GbE</td>
			<td style="width: 130px; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">ppc-n[0-4]</td>
		</tr>
	</tbody>
</table>

