# File Transfer Methods

## SFTP Clients

### Cyberduck

CyberDuck is a popular open source SFTP client for Windows and Mac. 
Download and install Cyberduck. Open the Cyberduck application. 
Click the "Open Connection" button on the toolbar.

![](../_assets/img/data-management/cyberduck.png)

- Select `SFTP (SSH File Transfer Protocol)` from the drop-down.
- Enter htc.crc.pitt.edu in the `Server` box.
- Enter your Pitt username and password, and click `Connect`.

Your files on the server will appear in the CyberDuck window. 
You can now drag-and-drop files to and from the window to upload/download files.

### FileZilla

FileZilla is cross-platform FTP application available for Windows, Linux, and macOS. 
Download and install FileZilla. Open the FileZilla application.

![](../_assets/img/data-management/FileZilla.png)

- Enter `sftp://htc.crc.pitt.edu` in the `Host` box.
- Enter your Pitt username and password, and click `QuickConnect`.

Your files on the server will appear in the FileZilla window. 

### The Open OnDemand File App

!!! warning The Open OnDemand is not suitable for larger files ( > 1 GB ) due to a limited cache size.

- Log on to `ondemand.htc.crc.pitt.edu` 
- Click `Files` -> `Home Directory` 
- Click `Upload` and choose File(s) from your computer.

![](../_assets/img/data-management/ondemand.png)

### Globus File Sharing

For large data sets, consider using [Globus](file-transfer-methods/globus.md).

An institutional endpoint is **not required** to use Globus; You can set up a personal endpoint on your computer if you 
need to transfer large amounts of data.

## Command Line Tools

### rsync

You can use `rsync` from a terminal/shell on your local computer.

Copy **to** the cluster:
`rsync -aP {files} fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/`

Where {files} is a local directory on your computer. This makes a recursive copy of the local files to a folder on the 
cluster.


Copy **from** the cluster:
`rsync -aP fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/files/ .`

This copies the `/bgfs/sam/fangping/files` folder on the cluster to the working directory on the local machine.

### scp
`scp` is similar to `rsync` in that it runs in a terminal on your local machine.

`scp -r files fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/`

This copies the folder files in the current directory on the computer you issued the command on to a folder on the cluster.

`scp -r fangping@htc.crc.pitt.edu:/bgfs/sam/fangping/files/ .`

This copies the `/bgfs/sam/fangping/files` folder on the cluster to the current directory on the computer you issued 
the command from.

### aspera

Aspera is IBM's high-performance file transfer software which allows for the transfer large files and data sets with 
predictable, reliable and secure delivery regardless of file size or transfer distance from a location that has the 
aspera transfer server running.&nbsp; The NCBI recommend the use of aspera for transfer of data sets from their site.

Download aspera from to your home directory on HTC cluster.

Download link: https://download.asperasoft.com/download/sw/connect/3.9.1/ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.tar.gz

`tar xzvf ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.tar.gz`

You will find file `ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.sh`:

`./ibm-aspera-connect-3.9.1.171801-linux-g2.12-64.sh`

aspera is installed to `~/.aspera`

You can then use aspera to download files. 
The aspera binary is at `~/.aspera/connect/bin/ascp` and the key is at `~/.aspera/connect/etc/asperaweb_id_dsa.openssh`
```commandline
[fangping@login0b aspera]$ ~/.aspera/connect/bin/ascp -QT -l 300m -P33001 -i ~/.aspera/connect/etc/asperaweb_id_dsa.openssh era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/SRR949/SRR949627/SRR949627_1.fastq.gz .
```

## Using wget or curl to Download Files

wget is a networking command-line tool that lets you download files. It supports the HTTP,HTTPS, FTP, and FTPS internet protocols.

In order to download a file using Wget, type wget followed by the URL of the file that you wish to download. Wget will download the file in the given URL and save it in the current directory.

`wget https://domain.com/file`

The equivalent curl command is:

`curl https://domain.com/file`

The file will keep the name embedded in the url.
You can specify a destination name.

`curl -o newname https://what.ever`

`wget -O newname https://domain.com/file.`

If your url includes specific characters, such as ?, you can put the full url in single quotes to prevent the shell 
from mangling these characters.

`wget -O newname 'https://domain.com/file?userId=id&Credential=password’`

`curl -o newname 'https://domain.com/file?userId=id&Credential=password’`


## Cloud Tools

### Pitt OneDrive

You can transfer data between Pitt OneDrive and the cluster. 
See [this page](file-transfer-methods/microsoft-onedrive.md) for details.

### AWS S3

`awscli` has been installed as a module in Lmod. 
You can transfer data from the cluster to AWS S3.

`module load awscli/1.16.135`

`aws s3 sync /bgfs/sam/fangping/DataUpload s3://my-s3-bucket/data_from_htc`

This will transfer `/bgfs/sam/fangping/DataUpload` folder to `s3://my-s3-bucket/data_from_htc`

You can submit this command as a job on HTC computational node.

### google bucket

You can install and configure `gsutil` to transfer data from/to gs bucket.

`module load gsutil/5.16`

`gsutil config`

`gsutil cp -r gs://gs-bucket-name/ .`

This will recursively transfer `gs://gs-bucket-name/` folder to the current folder.

### Azure Storage

[AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10) is a command-line 
tool that moves data into and out of Azure Storage. `azcopy` has been installed as a module.

`module load azcopy/10.11.0`
`azcopy --help`

`azcopy copy "/bgfs/your/folder/" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&amp;ss=bjqt&amp;srt=sco&amp;sp=rwddgcup&amp;se=2019-05-01T05:01:17Z&amp;st=2019-04-30T21:01:17Z&amp;spr=https&amp;sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true`

This example command recursively copies data from a local directory to a blob container. A fictitious SAS token is 
appended to the end of the container URL.

You can submit this command as a job on HTC computational node.
