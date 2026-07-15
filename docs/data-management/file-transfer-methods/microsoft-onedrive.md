# Microsoft OneDrive

## Accessing Microsoft OneDrive

Reach your Pitt OneDrive from [portal.office.com](https://portal.office.com),
directly at [pitt.onedrive.com](https://pitt.onedrive.com), or via
[my.pitt.edu](https://my.pitt.edu) — sign in with your Pitt credentials. A few
notes on this storage:

- Any Pitt faculty, student, or staff member can activate OneDrive by logging in.
- Under the University agreement you get **5 TB** (max file size 250 GB),
  increasable up to **25 TB** via a ticket to the Pitt IT Help Desk; beyond that,
  additional 25 TB accounts/sites can be created.
- OneDrive is **not** suitable for sensitive data, including personal information.

You can also move data between OneDrive and CRCD storage
[using Globus](globus-microsoft-onedrive.md). The rest of this page covers the
command-line approach with `rclone`.

## Rclone

[Rclone](https://rclone.org) is a command-line tool for transferring and syncing
files between local storage and OneDrive (and many other services), often faster
than `scp` or `rsync`. Load the CRCD module (run `module spider rclone` to see
available versions):

```bash
module load rclone
```

### Configuring rclone for OneDrive

Configuring OneDrive is hard on a headless machine because it needs a browser for
authentication, so do it from an **HTC Desktop** session on Open OnDemand: log on
to `ondemand.htc.crc.pitt.edu`, launch **Interactive Apps → HTC Desktop** (1 core,
1 hour), then open a terminal emulator and run:

```bash
module load rclone
rclone config
```

This creates `~/.config/rclone/rclone.conf`. Answer the prompts:

1. **New remote** → `n`
2. **Name** → something short, e.g. `odrive`
3. **Storage** → the number for **Microsoft OneDrive** (`onedrive`)
4. **client_id / client_secret** → leave blank (press Enter)
5. **region** → `1` (Microsoft Cloud Global); leave **tenant** blank
6. **Advanced config?** → `n`
7. **Use auto config?** → `y` (a browser opens for Pitt Passport authentication)
8. **Drive type** → `1` (OneDrive Personal or Business)
9. Select the listed business drive (`0`), confirm, then **quit** with `q`

??? note "Example `rclone config` session"
    ```
    [fangping@htc-n1 ~]$ rclone config
    e/n/d/r/c/s/q> n
    name> odrive
    Storage> 38          # Microsoft OneDrive ("onedrive")
    client_id>           # (blank)
    client_secret>       # (blank)
    region> 1            # Microsoft Cloud Global
    tenant>              # (blank)
    Edit advanced config? y/n> n
    Use auto config? y/n> y
    # ... browser opens; log in with Pitt Passport and authorize rclone ...
    Your choice> 1       # OneDrive Personal or Business
    Chose drive to use:> 0
    Is that okay? y/n> y
    y/e/d> y
    e/n/d/r/c/s/q> q
    ```

### Rclone usage

The name `odrive` below is whatever you chose during configuration. The trailing
colon tells rclone it's a remote, not a local path.

```bash
rclone lsd odrive:                          # list top-level folders
rclone ls odrive:                           # list all files
rclone mkdir odrive:backup                  # create a folder
rclone copy <local file> odrive:backup      # copy a file up
rclone copy <local folder> odrive:backup/newfolder   # copy a folder up
rclone sync <local folder> odrive:backup    # full sync (overwrites/deletes at destination!)
```

!!! warning
    `rclone sync` makes the destination match the source — files already in
    `odrive:backup` may be overwritten or deleted. Double-check the destination
    before syncing.

A few useful options: `--transfers=N` (parallel transfers, default 4),
`--drive-chunk-size=SIZE`, `--drive-use-trash` (send deletions to OneDrive's
trash), and `--skip-links` (ignore symlinks). Full list in the
[rclone docs](https://rclone.org/docs/).

### Automating transfers with a batch job

Scripting rclone lets you schedule periodic backups. Submit a job like:

```bash
#!/bin/bash
#SBATCH --job-name=rclone_to_odrive
#SBATCH --cluster=htc
#SBATCH --partition=htc
#SBATCH --cpus-per-task=1
#SBATCH --time=1-00:00:00
#SBATCH --output=rclone_to_odrive.out

module purge
module load rclone

rclone copy --skip-links <local folder> odrive:<remote folder>
```

## Accessing a OneDrive shared folder

You **cannot** download OneDrive *shared* files with rclone
([rclone issue #4062](https://github.com/rclone/rclone/issues/4062)). Instead use
the [OneDrive Client for Linux](https://github.com/abraunegg/onedrive), available
as a module:

```bash
module load onedrive
onedrive --list-shared-folders
```

To sync a shared folder, list it in `~/.config/onedrive/business_shared_folders`
(one folder name per line), then:

```bash
onedrive --synchronize --sync-shared-folders --download-only --single-directory '<folder>' --verbose
```

## Sharing files from OneDrive

You can share OneDrive files and folders with anyone and control whether they can
edit, comment, or view — see
[Microsoft's sharing guide](https://support.microsoft.com/en-us/office/share-onedrive-files-and-folders-9fcc2f7d-de0c-4cec-93b0-a82024800c07).
OneDrive's own program policies apply to shared content.

## Related

<div class="grid cards" markdown>

-   :material-transit-connection-variant:{ .lg .middle } __OneDrive via Globus__

    ---

    A graphical alternative for moving data to and from OneDrive.

    [:octicons-arrow-right-24: Globus for OneDrive](globus-microsoft-onedrive.md)

-   :material-swap-horizontal:{ .lg .middle } __Other transfer methods__

    ---

    SFTP clients, rsync/scp, Globus, and other cloud tools.

    [:octicons-arrow-right-24: File Transfer Methods](../index.md)

</div>
