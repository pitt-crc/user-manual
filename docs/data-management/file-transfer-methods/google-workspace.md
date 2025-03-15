# Google Workspace Drive

## Accessing Google Workspace
As an additional resource, please refer to the 
[google workspace documents on Pitt Information Technology page](https://www.technology.pitt.edu/services/google-workspace).

Go to [account.google.com](http://account.google.com) and use your Pitt email address and your account will automatically 
fall under the University agreement.

New policy from Google:

<p><span style="color:#FF0000;">The new storage model will provide schools and universities with a baseline of 100TB 
of pooled cloud storage shared across all of your users — more than enough storage for over 100 million docs, 8 million 
presentations or 400,000 hours of video. This policy will go into effect across all Google Workspace for Education 
editions for existing customers in July 2022 and will be effective for new customers signing up in 2022.</span></p>

A few notes about this storage:

<ul>
	<li>Any University faculty, student or staff can activate and access their official University of Pittsburgh 
        GSuite Drive by visiting the above link and logging in.</li>
	<li>Under the university agreement, University of Pittsburgh faculty, staff and students have access to storage in 
        GSuite Drive; the maximum file size is 5TB.</li>
	<li>GSuite Drive is <strong>not</strong> suitable for storing sensitive data, including personal information.</li>
</ul>

## Rclone
[Rclone](http://rclone.org) is a command-line program that supports file transfers and syncing of files 
between local storage and Google Drive as well as a number of other storage services. Rclone offers options to optimize 
a transfer and reach higher transfer speeds than other common transfer tools such as scp and rsync.

If you wish to use rclone to transfer files to or from CRC file systems, you can use the CRC installation of rclone. 
There is a module to set the proper environment to use the tool. 
To use it, you first need to load the module:
```commandline
module load rclone/1.53.2
```

## Configuration of Rclone
The next step is to configure rclone for the transfer partner. Below is the example to configure GSuite Drive. You 
must do the configuration while you are on CRC login nodes.

### Configuration for transferring files to/from the University of Pittsburgh’s GSuite Drive storage
To configure (run the following command on CRC login nodes):
```commandline
[fangping@login0b ~]$ module load rclone/1.53.2

[fangping@login0b ~]$ rclone config
```

This command will create a ~/.config/rclone/rclone.conf file in your home directory which contains the setup 
information. You will be asked a few questions:

1. Choose ‘New remote’
```commandline
[fangping@login0b ~]$ rclone config
2020/11/23 14:06:46 NOTICE: Config file "/ihome/sam/fangping/.config/rclone/rclone.conf" not found - using defaults
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
```

2. Enter a name for the GSuite Drive. This will be typed out whenever you want to access the GSuiteDrive, so make it short.
```commandline
name> gdrive
```

3. Select Google Drive (“drive”)
```commandline
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 &lt;long list of types&gt;
12 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
13 / Google Drive
   \ "drive"
&lt;several more types&gt;
Storage> 13
```

4. Leave the Client ID and Client Secret fields blank--just press enter
```commandline
<information>
Enter a string value. Press Enter for the default ("").
client_id>
<information>
client_secret>
<more information>
```

5. Scope - choose 1 (full access)
```commandline
<information on Scope>
scope> 1
```

6. ID of root folder and Service account credentials - just press enter
```commandline
<information>
root_folder_id>
<information>
service_account_file>
```
7. Advanced configuration - choose no
```commandline
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n
```

8. Choose ‘No for auto-config. (you are working on a remote or headless machine)
```commandline
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> n
```

9. Please go to the following link: 
<a href="https://accounts.google.com/o/oauth2/auth?access_type=offline&amp;client_id=&lt;details&gt;">https://accounts.google.com/o/oauth2/auth?access_type=offline&amp;client_id=...</a></p>

Log in and authorize rclone for access

Open a web browser and paste the link. Logon your Pitt associated google account.


![GOOGLE-WORKSPACE1](../_assets/img/data-management/google_workspace_1.png)

Choose Allow to allow rclone access. A code is shown. Please copy this code, switch to your application and paste it there.

```commandline
Enter verification code> <paste your code>
```

10. You should select no to the team drive question.
```commandline
Configure this as a team drive?
y) Yes
n) No (default)
y/n> n
```

11. Choose Yes to confirm the configuration, and then quit the config
```commandline
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y

Current remotes:

Name                 Type
====                 ====
gdrive               drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q
```
Congrats, you can now access your GSuite Drive via rclone!

## Rclone Usage

<p>In this section some common rclone usage cases are presented. In the following the name gdrive is being used. You would need to use the name you choose when doing your configuration. Note the trailing colon. This indicates to rclone that “gdrive” is a remote storage system, rather than a file or directory in your current working directory. At any point, you may verify that these changes were successful by viewing your Drive from within a web browser.</p>

<ul>
	<li><strong>List top-level buckets in your Drive</strong>: rclone lsd gdrive:</li>
	<li><strong>List all files</strong> <strong>in your Driv</strong>e: rclone ls gdrive:</li>
	<li><strong>Create backup bucket on GSuite Drive: </strong>rclone mkdir gdrive:backup</li>
	<li><strong>Copy a file</strong> <strong>from CRC to gdrive:backup bucket</strong>: rclone copy &lt;local file&gt; gdrive:backup</li>
	<li><strong>Copy contents of a folder to gdrive:backup bucket</strong>: rclone copy &lt;local folder&gt; gdrive:backup/newfolder</li>
	<li><strong>Synchronizing directories is done with the sync option</strong>. rclone sync &lt;local folder&gt; gdrive:backup
	<ul>
		<li>This is a full synchronization, so <strong>files at the destination (gdrive:backup) prior to the sync will be overwritten or deleted</strong>. Double check the destination and its contents.</li>
	</ul>
	</li>
</ul>

## Rclone Options

<p>While the full list of options can be found in the official MANUAL file in the <a href="https://github.com/ncw/rclone">Rclone github repo</a> (or ‘man rclone’ if rclone is installed), some options are:</p>

<ul>
	<li>--transfers=N (default N=4)</li>
</ul>

<p>Number of file transfers to be run in parallel. Increasing this may increase the overall speed of a large transfer, as long as the network and remote storage system can handle it (bandwidth and memory).</p>

<ul>
	<li>--drive-chunk-size=SIZE (default SIZE=8192)</li>
</ul>

<p>The chunk size for a transfer in kilobytes; must be a power of 2 and at least 256. Each chunk is buffered in memory prior to the transfer, so increasing this increases how much memory is used.</p>

<ul>
	<li>--drive-use-trash</li>
</ul>

<p>Sends files to Google Drive’s trash instead of deleting (prior to a directory sync for instance). Note that this is not a default option, because the Trash is not accessible through Rclone and must be managed through a web browser.</p>

<ul>
	<li>-- --skip-links</li>
</ul>

<p>Ignore symlinks.</p>

<h2>Additional Important Considerations</h2>

<ul>
	<li>There are daily limits on the amount of data that a user can transfer to/from GSuite Drive. The limits have been determined to be an upload limit of 750GB/day, and a download limit between 9 and 10TB/day.</li>
	<li><strong>Google limits transfers to about 2 files per second.</strong> This may cause uploads of many small files to be much slower than the upload rate. However, it will not stop the transfer and will continue to retry files that were blocked by Google’s rate limit. Considering compressing small files into a single larger file if this becomes a problem.</li>
</ul>

<h2>Automating Transfers Using Rclone</h2>

<p>The file transfer process can be scripted, and you can submit the script as a batch job. With these scripts a user can set up a periodic backup of their data.</p>

<p>Submit the following job to transfer a folder to GSuite Drive:</p>

```shell
#!/bin/bash
#
#SBATCH --job-name=rclone_to_gdrive
#SBATCH -c 1
#SBATCH -t 1-00:00 # Runtime in D-HH:MM
#SBATCH --output=rclone_to_gdrive.out

module purge
module load rclone/1.53.2

rclone copy --skip-links <local folder> gdrive:<remote folder>
```


<h1>Share files from Google Drive</h1>

<p>You can share the files and folders that you store in Google Drive with anyone.</p>

<p><a href="https://support.google.com/drive/answer/2494822">https://support.google.com/drive/answer/2494822</a></p>

<p>When you share from Google Drive, you can control whether people can edit, comment on, or only view the file. When you share content from Google Drive, the Google Drive program policies apply.</p>

<h2>Transferring ownership of files</h2>

<p>If you store your data in an individual account when you leave Pitt you may need to transfer your files to another user or your major professor.</p>

<p>These are the Google instructions on doing so:</p>

<p><a href="https://support.google.com/drive/answer/2494892">https://support.google.com/drive/answer/2494892</a></p>

<div class="field field-name-field-builder field-type-builder field-label-hidden">
<div class="field-items">
<div class="field-item even">
<div class="builder-section builder-section-0"><!-- Builder wrapper -->
<div class="builder-wrapper buider-wrapper-1816">&nbsp;</div>
<!-- // builder wrapper --></div>
</div>
</div>
</div>

