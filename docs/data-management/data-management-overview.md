# Data Management Overview

<h2>File Systems</h2>

There are several distinct file spaces available on clusters, each serving a different function.

<ul>
	<li><span style="font-family:courier new,courier,monospace;">/ihome </span>($HOME), the system housing home directories.

	<ul>
		<li>It contains sub-directories for each user group, and those contain individual user directories.</li>
		<li>Also referred to as the 'login directory' and is the entry point when a user logs into the CRC ecosystem</li>
		<li>75 GB quota that cannot be increased</li>
	</ul>
	</li>
	<li><span style="font-family:courier new,courier,monospace;">/ix</span> and <span style="font-family:courier new,courier,monospace;">/ix1</span>, enterprise storage locations for persistent file storage.
	<ul>
		<li>5 TB provisions for new allocations can be found here.</li>
		<li>There is no charge for a 5 TB provision on ix.</li>
		<li>If you do not have a location provisioned, <a href="https://crc.pitt.edu/webforms-and-requests/storage-request-guidelines-and-webform">please let us know with this form</a>.</li>
		<li>5 TB increments of extra storage are available for purchase at a subsidized rate of $65 per TB per year.</li>
	</ul>
	</li>
	<li><span style="font-family:courier new,courier,monospace;">/bgfs</span>, a BeeGFS file system for persistent file storage that is no longer being provisioned on.</li>
	<li><span style="font-family:courier new,courier,monospace;">/zfs1</span> and <span style="font-family:courier new,courier,monospace;">/zfs2</span>, 1 PB file systems for archival storage.</li>
</ul>

You can check your usage and quotas across these filesystems with the wrapper script <span style="font-family:courier new,courier,monospace;">crc-quota</span>:

<pre>
[nlc60@login0b ~] : crc-quota
User: 'nlc60'
-&gt; ihome: 59.63 GB / 75.0 GB

Group: 'sam'
-&gt; zfs1: 1.76 TB / 5.0 TB
-&gt; beegfs: 14.68 TB / 80.0 TB</pre>

&nbsp;

<h2>File permissions</h2>

Group shared folders on /ix, /bgfs, /zfs1, or /zfs2 can be requested via ticket. The top level folder is owned by 
<span style="font-family:courier new,courier,monospace;">root:groupname</span> with 2770 permission. The 2 sets the 
sticky bit, group members have read + write permission, 
and&nbsp;<span style="font-family:courier new,courier,monospace;">other</span> users cannot access the folder.

<ul>
	<li>Access to files by the group members is governed by Unix file permissions.&nbsp;For detailed information on Unix file protections, see the man page for the&nbsp;chmod&nbsp;command.&nbsp;&nbsp;</li>
	<li>To share files with your group, give the group read and execute access for each directory from your top-level directory down to the directory that contains the files you want to share.&nbsp;</li>
	<li>chmod g+rx directory-name</li>
	<li>Then give the group read and execute access to each file you want to share.</li>
	<li>chmod g+rx filename</li>
	<li>To give the group the ability to edit or change a file, add write access to the group:</li>
	<li>chmod g+rwx filename</li>
	<li>If you want to share file with other group members or users, submit a help ticket and we can use Access Control Lists (ACLs) – a more fine-grained control than Unix file permissions allow. The command for bgfs is setfacl, and for ZFS is nfs4_setfacl. There are man pages for chmod, setfacl and nfs4_setfacl.</li>
</ul>

&nbsp;

<h2>Shared Folders</h2>

iX storage locations are only accessible to allocation owners and the users they sponsor (their "user group").

If you are working with collaborators that also have CRC user&nbsp;accounts, but are not in your user group, a shared folder 
under your iX location can be used to facillitate sharing data between your groups.&nbsp;

These should be requested by 
<a href="https://crc.pitt.edu/webforms-and-requests/submitting-help-ticket-guidelines-and-submission-form">submitting a ticket</a> and providing details about the users that need access.

This should include Pitt Usernames and&nbsp;specifics about the permssions they will require (read, write, execute, etc).&nbsp;

&nbsp;

<h2>Restoring Accidentally Deleted Files</h2>

Both ixSystems and ZFS keep snapshots for up to 7 days.

<a href="https://crc.pitt.edu/webforms-and-requests/submitting-help-ticket-guidelines-and-submission-form">Submit a help ticket</a> with your request to restore data from snapshots.&nbsp;

<strong>Snapshots are not available for BGFS.</strong>

&nbsp;

<h2>Moving Data between your local machine and the CRC Clusters</h2>

<h3>SFTP Clients&nbsp;</h3>

### CyberDuck

CyberDuck is a popular&nbsp;open source SFTP client for Windows and Mac. Download and install Cyberduck. Open the Cyberduck 
application. Click the "Open Connection" button on the toolbar.

![](../_assets/img/data-management/cyberduck.png)

<ul>
	<li>Select "SFTP (SSH File Transfer Protocol)" from the drop-down.</li>
	<li>Enter htc.crc.pitt.edu in the&nbsp;Server&nbsp;box.</li>
	<li>Enter your Pitt username and password, and click&nbsp;Connect.</li>
</ul>

Your files on the server&nbsp;will appear in the CyberDuck window. &nbsp;You can now drag-and-drop files to and from the window 
to upload/download files.

&nbsp;

<h3><a id="Filezilla" name="Filezilla">FileZilla</a></h3>

FileZilla is cross-platform FTP application available for Windows, Linux, and macOS. Download and install FileZilla. 
Open the FileZilla application.

![](../_assets/img/data-management/FileZilla.png)

<ul>
	<li>Enter sftp://htc.crc.pitt.edu in the Host&nbsp;box.</li>
	<li>Enter your Pitt username and password, and click&nbsp;QuickConnect.</li>
</ul>

Your files on the server&nbsp;will appear in the FileZilla window. &nbsp;

<h3>&nbsp;</h3>

<h3>Web Tools</h3>

<h3><a id="OOD" name="OOD">Ondemand File App</a></h3>

Logon ondemand.htc.crc.pitt.edu, Click Files -&gt; Home Directory, Click Upload and choose File(s) from your computer. 
Due to limited cache size, DO NOT use Ondemand File App to upload big files ( &gt; 1 GB).

![](../_assets/img/data-management/ondemand.png)

<h3>&nbsp;</h3>

<h3><a id="Globus" name="Globus">Globus</a></h3>

For large data sets, consider 
using <a href="https://crc.pitt.edu/user-manual/data-management/guide-globus-file-transferring-and-sharing">Globus</a>.

An institutional endpoint is<strong> not required</strong> to use Globus; You can set up a personal endpoint on your 
computer if you need to transfer large amounts of data.

&nbsp;

<h3><a id="Command Line Tools" name="Command Line Tools">Command Line Tools</a></h3>

<h3>rsync</h3>

From a terminal/shell on your computer, you will issue your rsync command.

rsync -aP files fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/

This makes a recursive copy of local directory files on the computer you issued the command on to a folder on the 
cluster. -P is --partial --progress.<br />
rsync -aP fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/files/ .

<br />
This copies the /bgfs/sam/fangping/files folder on the cluster to the current directory on the computer you issued the command.

<h3>&nbsp;</h3>

<h3>scp</h3>

From a terminal/shell on your computer, you will issue your SCP command.

scp -r files fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/

This copies the folder files in the current directory on the computer you issued the command on to a folder on the cluster.

scp -r <a href="mailto:fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/">fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/</a>files/ .

This copies the /bgfs/sam/fangping/files folder on the cluster to the current directory on the computer you issued the command.

<h3>&nbsp;</h3>

<h3>aspera</h3>

Aspera is IBM's high-performance file transfer software which allows for the transfer large files and data sets with 
predictable, reliable and secure delivery regardless of file size or transfer distance from a location that has the 
aspera transfer server running.&nbsp; The NCBI recommend the use of aspera for transfer of data sets from their site.

Download aspera from to your home directory on HTC cluster.

Download link: https://download.asperasoft.com/download/sw/connect/3.9.1/ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.tar.gz

tar xzvf ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.tar.gz

You will find file ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.sh

./ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.sh

aspera is installed to ~/.aspera

You can then use aspera to download files. 
aspera binary is at ~/.aspera/connect/bin/ascp and the key is at ~/.aspera/connect/etc/asperaweb_id_dsa.openssh

[fangping@login0b aspera]$ ~/.aspera/connect/bin/ascp -QT -l 300m -P33001 -i ~/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/SRR949/SRR949627/SRR949627_1.fastq.gz .

<h3>&nbsp;</h3>

<h3><a id="cloud resources" name="cloud resources">Cloud Resources</a></h3>

<h3>Pitt OneDrive</h3>

You can transfer data between Pitt OneDrive and the cluster. 
Follow <a href="https://crc.pitt.edu/user-support/resource-documentation/onedrive">these steps</a>.

### Pitt Box

You can transfer data from and to Pitt box from the cluster. Follow <a href="/accessing-box-cluster">these steps</a>.

<h3>&nbsp;</h3>

<h3>AWS S3</h3>

awscli has been installed as a module. You can transfer data from the cluster to AWS S3.

module load awscli/1.16.135

aws s3 sync /bgfs/sam/fangping/DataUpload s3://my-s3-bucket/data_from_htc

This will transfer /bgfs/sam/fangping/DataUpload folder to s3://my-s3-bucket/data_from_htc

You can submit this command as a job on HTC computational node.

<h3>&nbsp;</h3>

<h3>google bucket</h3>

You can install and configure gsutil to transfer data from/to gs bucket.

module load gsutil/5.16

gsutil config

gsutil cp -r gs://gs-bucket-name/ .

This will recursively transfer gs://gs-bucket-name/ folder to the current folder.

<h3>&nbsp;</h3>

<h3>Azure Storage</h3>

<a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10">AzCopy</a> is a command-line 
tool that moves data into and out of Azure Storage. azcopy has been installed as a module.

module load azcopy/10.11.0<br />
azcopy --help

azcopy copy "/bgfs/your/folder/" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&amp;ss=bjqt&amp;srt=sco&amp;sp=rwddgcup&amp;se=2019-05-01T05:01:17Z&amp;st=2019-04-30T21:01:17Z&amp;spr=https&amp;sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true

This example command recursively copies data from a local directory to a blob container. A fictitious SAS token is 
appended to the end of the container URL.

<br />
You can submit this command as a job on HTC computational node.
