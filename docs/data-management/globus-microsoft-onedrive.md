# Globus for Microsoft OneDrive

## How to Access OneDrive in Globus

Globus can be used to access your files on Microsoft OneDrive and SharePoint. Connect from your computer 
using [Globus Connect Personal](https://www.globus.org/globus-connect-personal) or sign in to 
[Globus File Manager](https://app.globus.org/) with your Pitt credentials. Search for the collection 
named “UPitt-OneDrive” and select it:

![](../_assets/img/data-management/gonedrive1.png)

You may see prompts asking you to verify that you wish to grant access. Complete that process then you will see the 
files within your OneDrive space and be able to copy files to or from it just like any other Globus collection. Your 
OneDrive space is shown as the path “My files” in the UPitt-OneDrive collection:

![](../_assets/img/data-management/gonedrive2.png)

If you would like to transfer data between CRC storage and OneDrive, search for the collection named “pitt#dtn” in the 
right side of Collection and logon using your Pitt crendentials. Go to your /ix, /bgfs or /ihome folder in Path.

![](../_assets/img/data-management/gonedrive3.png)

## How to Access OneDrive Shared Folders in Globus

If someone has shared a folder from their OneDrive with you, that folder can be found by clicking the “up one folder” 
button and then looking in your “Shared” folder:

![](../_assets/img/data-management/gonedrive4.png)

## How to Access SharePoint Sites in Globus

SharePoint sites can also be browsed within Globus. However, you must first “follow” the site before it will be visible 
to Globus. To do that, log into [the Pitt IT SharePoint service](https://pitt.sharepoint.com/_layouts/15/sharepoint.aspx) 
with your Pitt credentials. Next, navigate to the SharePoint site you would like to browse. Then, click the star icon 
next to the site you wish to follow:

![](../_assets/img/data-management/gonedrive5.png)

Finally, return to Globus and click the “up one folder” button until you are able to see the “Shared libraries” folder:

![](../_assets/img/data-management/gonedrive6.png)

The SharePoint site will now be available under the path “Shared libraries”:

![](../_assets/img/data-management/gonedrive7.png)

## OneDrive and SharePoint Limitations

OneDrive and SharePoint have limitations on what files can be stored, which may affect how you use the service. This is 
of particular importance if you upload files from Linux systems such as Globus endpoints pitt#dtn or "pitt crc dtn 5.4” 
provided by CRC.

<ol>
	<li value="NaN">OneDrive does not support empty (zero-byte) files. Transferring a zero-byte file will result in a “storage quota exceeded” error with the message “OneDrive does not support creation of empty files”.</li>
	<li value="NaN">OneDrive does not support symbolic links. When you attempt to upload a file that is a symlink, OneDrive will instead upload the contents of the file to which the symlink points. In other words, if you upload the file “a.txt” and file “symlink-to-a.txt”, both files in OneDrive will be identical. The latter would no longer be a symlink but just a regular file with the same content as a.txt.</li>
	<li value="NaN">OneDrive does not support POSIX file permissions or access control lists (ACLs). When you upload a file to OneDrive and then later download that file, the file will no longer have any of the custom permissions or ACLs that the original file had. If you require specific permissions on your files, you will need to modify the permission of the files (e.g., chmod) after they have been downloaded from OneDrive/SharePoint.</li>
</ol>

To store these unsupported file types, you may choose to create a tar archive or zip your files then transfer that 
file to OneDrive.
