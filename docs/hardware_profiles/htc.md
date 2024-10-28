<table border="1" cellpadding="1" cellspacing="1" style="width: 910px;">
	<thead>
		<tr>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Partition</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Architecture</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">--constraint</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Nodes</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Cores/Node</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Mem/Node</th>
			<th scope="col" style="border-color: rgb(170, 170, 170);">Mem/Core</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 78px;">Scratch</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 50px;">Network</th>
			<th scope="col" style="border-color: rgb(170, 170, 170); width: 93px;">Nodes</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td colspan="1" rowspan="4" style="vertical-align: top; background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="font-family:courier new,courier,monospace;">htc</span></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.amd.com/en/products/cpu/amd-epyc-9374f">AMD EPYC 9374F (Genoa)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">amd, genoa</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">20</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">768 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">12 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 78px;">3.2 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 50px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 93px;">htc-n[50-69]</td>
		</tr>
		<tr>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html">Intel Xeon Platinum 8352Y (Ice Lake)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">intel, ice_lake</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">18</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">512 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 78px;">2 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 50px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 93px;">htc-n[32-49]</td>
		</tr>
		<tr>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://www.intel.com/content/www/us/en/products/sku/212284/intel-xeon-platinum-8352y-processor-48m-cache-2-20-ghz/specifications.html">Intel Xeon Platinum 8352Y (Ice Lake)</a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">intel, ice_lake</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">4</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">64</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><span style="color:#000000;">1 TB</span></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 78px;">2 TB NVMe</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 50px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 93px;">htc-1024-n[0-3]</td>
		</tr>
		<tr>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);"><a href="https://ark.intel.com/content/www/us/en/ark/products/199351/intel-xeon-gold-6248r-processor-35-75m-cache-3-00-ghz.html">Intel Xeon Gold 6248R&nbsp;(<em>Cascade Lake)</em></a></td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">intel, cascade_lake</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">8</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">48</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">768 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170);">16 GB</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 78px;">960 GB SSD</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 50px;">10GbE</td>
			<td style="background-color: rgb(255, 255, 255); border-color: rgb(170, 170, 170); width: 93px;">htc-n[24-31]</td>
		</tr>
	</tbody>
</table>

