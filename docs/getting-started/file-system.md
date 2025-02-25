# File Systems and Data Management

## Linux File Permissions and Ownership

Linux uses standard POSIX-style permissions to control who has the ability to read, write, or execute a file or a directory. The `ls -l` command will show the ownership and permissions of a file, a list of files, or all files in a directory.

![File-Permissions](../_assets/img/getting-started/file_permissions.png)

The Execute (x) permission on directories is very different compared to files. Essentially, you can think of it as providing access to the directory. Having execute permission on a directory authorizes you to look at extended information on files in the directory (using `ls -l`, for instance) but also allows you to change your working directory (using cd) or pass through this directory on your way to a subdirectory underneath. If we change the above output folder permissions to 744 (drwxr--r--), then sam group members and others cannot cd to this folder.

The `chmod` command is used to change the permissions of a file or directory. Only the User that owns a file may change ownership or permissions.

## Data Storage options in the CRCD Environment

### User Home Directories
Every individual user has a home directory (under `/ihome/<primary group>/$USER`, environment variable `$HOME`) for permanently storing code and important configuration files. Home Directories provide limited storage space (75 GB). Users can check their quota utilization using the `crc-quota` command. User home directories are backed up daily. Users can use command `id` to display user and group names along with their numeric IDs.

`/ihome/<primary group>` is owned by `root:<primary group>`, and the permissions are 750 (drwxr-x---). Only your group members can access `/ihome/<primary group>`. `/ihome/<primary group>/$USER` is owned by `$USER:<primary group>`, and the permissions are 755 (drwxr-xr-x).

### Research Project Space

The Research Project Space (under `/ix/<primary group>` or `/ix1/<primary group>`) provides data storage space for research projects. The default quota is 5TB. `/ix/<primary group>` or `/ix1/<primary group>` is owned by `<group owner>:<primary group>`, and the permissions are 770 (drwxrwx---). Your group members can access this folder and create their own subfolders. /ix or /ix1 folders are NOT backed up. We only keep 7-days snapshots.

## Default Permissions: umask

The default permissions set on a new file or directory is determined by the current setting of `umask`. This value is displayed with the umask command:
<code>
umask
0022
</code>

A value of 022 will allow group and others read access. The umask value is a mask that is applied to the permission mode of a file. That is, the numbers are subtracted to give the final permissions.

To change your default permissions, the umask command is also used: `umask 027` This value (027) of umask will cause a file to disallow world read access. This command can be added to your ~/.bashrc file to make the change permanent for all login and shell sessions.

Note that others can not access your group's `/ix/<primary group>` or `/ix1/<primary group>` folder. Others cannot access `/ihome/<primary group>`. You can use umask to share files with your group members.  Here is a list of suggestions for umask values.

002 the owner and members of the group have read/write access for files and read/write/execute access for directories; everybody else has read-only access to files and read/execute access to directories.

007 the owner and members of the group have read/write access for files and read/write/execute access for directories.

022 everyone has read-only access to files; everyone has read/execute access to directories. This is the default.

027 files are created with read-only access for members of the group, but only the owner has write access; directories are created with read/execute for the owner and group.

077 files are created as read/write only for the owner; directories are created read/write/execute only for the owner

## Sharing data

To share files on the cluster with other users, we recommend using file access control lists (ACLs) to share access to their data with others. The Linux commands are `nfs4_getfacl` and `nfs4_setfacl`. We recommend that users submit a help ticket to request a shared folder in the group's ix or ix1 folder. We will create a shared folder `/ix or ix1/<group name>/shared/<group name>_<user name>`. Both the group members and the user `<user name>` from other group will be granted rwx permissions to this folder `/ix/<group name>/shared/<group name>_<user name>`. The group members can copy files to this folder to share with the other user `<user name>`. The other user can directly cd to the shared folder `/ix/<group name>/shared/<group name>_<user name>`. The other user can cd to `/ix/<group name>` or `/ix/<group name>/shared`. However, the user will receive "permission denied" error if the user runs ls or other directory or file handling operations.
