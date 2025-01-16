# Hardware Investing Policy

The Pitt CRCD provides a computing hardware investment opportunity for 
research to further enable the work of the broader Pitt community and 
their own research groups. We maintain a separate category of CRCD 
Faculty users who do this called "Investors".

Rather than incurring the burden of purchasing, installing, and 
maintaining their own hardware, researchers can allocate grant or 
startup funds to the CRCD for community hardware purchases and the 
benefits stated below.


## Benefits of Investing in CRCD Resources

<link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">

<table class="display cell-border" id="nTable">
    <thead>
        <tr>
            <td>Benefit</td>
            <td>Description</td>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Professional Maintenance and Support</td>
            <td>All hardware is professionally managed by the Network Operations Center (https://www.technology.pitt.edu/network-operations-center-noc) at no charge to investors, and are administered with state of the art networking and security practices.</td>
        </tr>
        <tr>
            <td>Slurm Allocation Service Units</td>
            <td>Investors receive a 5-year user investment allocation on CRCD hardware, equivalent to 100% of the computational resources that they would have utilized had they purchased and housed hardware within their own group, enabling utilization of their investment in the context of the existing CRCD infrastructure.</td>
        </tr>
        <tr>
            <td>Bulk Volume Equipment Rates</td>
            <td>Investors can take advantage of lower equipment prices negotiated by CRCD based on the large volumes required for our cluster refreshes.</td>
        </tr>
        <tr>
            <td>Better Utilization and Energy Efficiency</td>
            <td>As the pool of resources available to the community increases, security is enhanced, and the energy consumed by the invested equipment is likely to be much lower than if the invested equipment had been hosted in individual labs.</td>
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

## How Investment Resources are Made Available to Investors

While the bulk of a whole-node computing resource investment is made available to the greater Pitt Research community 
(investors included), many investors require exclusive access to some component of their investment hardware. It is the 
CRC's policy that exclusive access can be maintained to up to **20 percent** of the hardware purchased by 
investment. For example, if a faculty member were to set investment funds towards **5 compute nodes**, 
**4 of them** will be made available to the community (again, also accessible to them), and 
**1 can be set aside** specifically for use by that Investor and their user group.
!!! note
    Investment hardware that has been made exclusive to a particular faculty member's group is housed and maintained 
    amongst the hardware that composes the rest of the cluster, and is correspondingly subject to downtime during 
    cluster-wide quarterly maintenance.

The investor will be guaranteed access to **100 percent of the equivalent in service units** (SUs) of the theoretical
total CPU hours from the contributed funds for 5 years (the expected lifetime of the hardware that the funds are used to purchase). The SUs will remain available if not expended by the faculty member for an additional 2 years after this 5 year period. The faculty member will be awarded a new allocation containing the full amount of SUs, separate from any existing proposal or standard allocation service units.

