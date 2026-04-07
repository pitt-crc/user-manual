# Resource Descriptions For Writing Proposals

## Useful Resource Information For Writing External Proposals

The information below may be of use to you in drafting proposals or other documents calling for
descriptions of Pitt CRCD computational resources available to researchers.

---

## Brief Text Description of CRCD Resources

The Pitt Center for Research Computing and Data (CRCD) provides access to computing hardware,
software, and research computing consulting to the University research community. The CRCD systems
are housed at the enterprise-level Network Operations Center (NOC) and are administered jointly with
Pitt Digital. Pitt Digital maintains the critical environmental infrastructure (redundant power, cooling,
networking, and security) and administers the cluster operating systems and storage backups. CRCD
interfaces directly with researchers and provides software installation services, training workshops, and
personalized consultation on improving software design/performance and computational workflows.
Connectivity between the NOC and main campus is via two 100 Gbps fibers and to Internet2 via 100 Gbps.
In terms of physical space, power, and cooling, the NOC has excess headroom for future expansion.
CRCD does not pay Pitt Digital for hosting, electricity, or other support charges. These data center costs
are covered by Pitt Digital as part of our joint campus cyberinfrastructure efforts to democratize access
to research computing.

The CRCD provides different types of hardware for different advanced computing needs, supporting the
shared memory processing (SMP), high throughput computing (HTC), message passing interface (MPI),
and GPU-accelerated computing paradigms.

---

## CPU Resources

The CPU-centric clusters include a total of **20,512 CPU cores**, comprised of the following:

**MPI cluster** — connected by NDR200G/HDR200G InfiniBand:

- 20 nodes of 128-core AMD EPYC 9575F (Turin) with 1.5 TB RAM and 3.2 TB NVMe local storage
- 136 nodes of 48-core Intel Xeon Gold 6342 (Ice Lake) with 512 GB RAM and 1.6 TB NVMe local storage

**HTC cluster** — all nodes connected by 10 GbE:

- 25 nodes of 128-core AMD EPYC 9575F (Turin) with 1.5 TB RAM and 3.2 TB NVMe local storage
- 20 nodes of 64-core AMD EPYC 9374F (Genoa) with 768 GB RAM and 3.2 TB NVMe local storage
- 18 nodes of 64-core Intel Xeon Platinum 8352Y (Ice Lake) with 512 GB RAM and 2 TB NVMe local storage
- 4 nodes of 64-core Intel Xeon Platinum 8352Y (Ice Lake) with 1 TB RAM and 2 TB NVMe local storage
- 8 nodes of 48-core Intel Xeon Gold 6248R (Cascade Lake) with 768 GB RAM and 1 TB SSD local storage

**SMP cluster** — all nodes connected by 10 GbE:

- 43 nodes of 64-core AMD EPYC 9374F (Genoa) with 768 GB RAM and 3.2 TB NVMe local storage
- 8 nodes of 64-core Intel Xeon Platinum 8352Y (Ice Lake) with 1 TB RAM and 12 TB NVMe local storage
- 2 nodes of 64-core Intel Xeon Platinum 8352Y (Ice Lake) with 2 TB RAM and 12 TB NVMe local storage
- 55 nodes of 32-core AMD EPYC 7302 (Rome) with 256 GB RAM and 1 TB SSD local storage

---

## GPU Resources

The GPU cluster provides access to **328 GPU instances** across 60 dedicated nodes:

- **2 HGX nodes** with 8× NVIDIA H200 141 GB SXM; NDR-200G and 25 GbE; dual-socket Intel Xeon Platinum 8592+ (128 cores); 3 TB RAM; 7 TB NVMe local storage
- **13 nodes** with 8× NVIDIA RTX PRO 6000 Blackwell 96 GB PCIe; NDR-200G and 25 GbE; dual-socket AMD EPYC 9555 (128 cores); 1.5 TB RAM; 7 TB NVMe local storage
- **20 nodes** with 4× NVIDIA L40S 48 GB PCIe; 10 GbE; dual-socket Intel Xeon Platinum 8462Y+ (64 cores); 512 GB RAM; 7 TB NVMe local storage
- **2 HGX nodes** with 8× NVIDIA A100 80 GB SXM NVLink; HDR-200G and 10 GbE; dual-socket AMD EPYC 7742 (128 cores); 1 TB RAM; 2 TB NVMe local storage
- **3 HGX nodes** with 8× NVIDIA A100 40 GB SXM NVLink; HDR-200G and 10 GbE; dual-socket AMD EPYC 7742 (128 cores); 1 TB RAM; 12 TB NVMe local storage
- **22 nodes** with 4× NVIDIA A100 40 GB PCIe; HDR-200G and 10 GbE; single-socket AMD EPYC 7742 (64 cores); 512 GB RAM; 3 TB NVMe local storage

The GPU cluster supports CUDA, TensorFlow, PyTorch, and other GPU-accelerated frameworks, and is
used for AI/ML training and inference, molecular dynamics simulations, genomics acceleration, and
large-scale scientific data analysis.

---

## Storage

The global storage infrastructure includes:

- A **4 PB all-flash VAST DataStore** providing high-performance storage for home directories (`/ihome`) and project data (`/vast`), with daily snapshots and 8-day retention
- A **6 PB iXsystems** enterprise storage tier (`/ix` and `/ix1`) for standard-tier project data, ZFS-backed with 12 TB NVMe caching and 7-day snapshots

All compute nodes are equipped with local NVMe scratch storage ranging from 960 GB to 14 TB per node.

---

## Secure Research Environment

In addition to the general-purpose computing environment, CRCD has provisioned a Secure Research
Environment (SRE) at the NIST SP 800-171 level (self-attestation), suitable for storing and computing
on NIH controlled-access datasets and Protected Health Information (PHI) containing HIPAA identifiers.
The SRE is currently comprised of:

- 5 nodes of 64-core AMD EPYC 9374F (Genoa) with 768 GB RAM and 3.2 TB NVMe local storage
- 2 nodes with 4× NVIDIA L40S 48 GB PCIe GPUs; 10 GbE; dual-socket Intel Xeon Platinum 8462Y+ (64 cores); 512 GB RAM; 7 TB NVMe local storage
- 600 TB iXsystems enterprise storage (ZFS-backed, self-encrypting drives)

There is a cost associated with usage of the SRE.

---

## Technical Support and Funding

The cluster compute nodes were purchased with funds provided by the University and by faculty
researchers, and are housed at the enterprise-level NOC, administered jointly with Pitt Digital.

The CRCD and Pitt Digital, in consultation with an advisory committee comprised of faculty from
various schools, are tasked with developing the road map for research computing to meet the emerging
needs of researchers at the University.

The CRCD PhD-level consultants form an interdisciplinary team with domain expertise spanning
computational chemistry, computational astrophysics, computational fluid dynamics, bioinformatics and
genomics, infectious disease modeling, data analytics, and machine learning, supported by a Research
Computing Specialist and a Coordinator of Outreach and Communications. The Pitt Digital team at the
NOC brings comprehensive expertise across data center operations including systems, networking, network
security, and monitoring.

---

## Software Environment

The cluster runs **Red Hat Enterprise Linux 9** with the **SLURM Workload Manager** provisioning
shared resources consistent with allocation, fair-share, and usage policies. Software packages are
managed at the user environment level with **Lmod**. Installed packages span a broad range of
scientific domains, including:

- **Quantum mechanics:** Gaussian, Molpro, VASP, CP2K, QMC
- **Classical mechanics:** NAMD, LAMMPS, Amber
- **Continuum mechanics:** COMSOL, ANSYS, Abaqus, Lumerical
- **Genomics:** Tophat/Bowtie, CLC Genomics Server, HISAT2, Cell Ranger, BWA, GATK
- **Data science and ML:** R, Python, CUDA, SAS, MATLAB, Mathematica, TensorFlow, Keras, PyTorch, Caffe

Many of these packages support CUDA, OpenACC, or other GPU-enabled frameworks.
