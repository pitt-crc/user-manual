# File Systems and Data Management

## Linux File Permissions and Ownership

Linux uses standard POSIX-style permissions to control who has the ability to read, write, or execute a file or a directory. The `ls -l` command will show the ownership and permissions of a file, a list of files, or all files in a directory.

![File-Permissions](../_assets/img/getting-started/file_permissions.png)

The Execute (x) permission on directories is very different compared to files. Essentially, you can think of it as providing access to the directory. Having execute permission on a directory authorizes you to look at extended information on files in the directory (using `ls -l`, for instance) but also allows you to change your working directory (using cd) or pass through this directory on your way to a subdirectory underneath. If we change the above output folder permissions to 744 (drwxr--r--), then sam group members and others cannot cd to this folder.

The `chmod` command is used to change the permissions of a file or directory. Only the User that owns a file may change ownership or permissions.

## Data Storage options in the CRCD Environment

### User Home Directories
Every individual user has a home directory (under `/ihome/<primary group>/$USER`, environment variable `$HOME`) for permanently storing code and important configuration files. Home Directories provide limited storage space (75 GB). Users can check their quota utilization using the `crc-quota` command. User home directories are backed up daily. Users can use command `id` to display user and group names along with their numeric IDs.

`/ihome/<primary group>` is owned by `root:<primary group>`, and the permissions are 750 (drwxr-x---). Only your group members can access `/ihome/<primary group>`. `/ihome/<primary group>/$USER` is owned by `$USER:<primary group>`, and the permissions are 755 ((drwxr-xr-x).

### Research Project Space

The Research Project Space (under `/ix/<primary group>` or `/ix1/<primary group>`) provides data storage space for research projects. The default quota is 5TB. `/ix/<primary group>` or `/ix1/<primary group>` is owned by `<group owner>:<primary group>`, and the permissions are 770 (drwxrwx---). Your group members can access this folder and create their own subfolders. /ix or /ix1 folders are NOT backed up. We only keep 7-days snapshots.

## umask


## Sharing data
TODO
