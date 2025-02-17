# File Systems and Data Management


![File-Permissions](../_assets/img/getting-started/file_permissions.png)

## Data Storage options in the CRCD Environment

### User Home Directories
Every individual user has a home directory (under `/ihome/<primary group>/$USER`, environment variable `$HOME`) for permanently storing code and important configuration files. Home Directories provide limited storage space (75 GB). Users can check their quota utilization using the `crc-quota` command. User home directories are backed up daily. Users can use command `id` to display user and group names along with their numeric IDs.

`/ihome/<primary group>` is owned by `root:<primary group>`, and the permissions are 750 (drwxr-x---). Only your group members can access `/ihome/<primary group>`. `/ihome/<primary group>/$USER` is owned by `$USER:<primary group>`, and the permissions are 755 ((drwxr-xr-x).
