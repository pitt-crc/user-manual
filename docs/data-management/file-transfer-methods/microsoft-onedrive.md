# Microsoft OneDrive

Reach your Pitt OneDrive from [portal.office.com](https://portal.office.com) (now branded
Microsoft 365), directly at [pitt.onedrive.com](https://pitt.onedrive.com), or via
[my.pitt.edu](https://my.pitt.edu) — sign in with your Pitt credentials. A few notes on this
storage:

- Any Pitt faculty, student, or staff member can activate OneDrive by logging in.
- Under the University agreement you get **5 TB** (max file size 250 GB), increasable up to
  **25 TB** via a ticket to the Pitt IT Help Desk; beyond that, additional 25 TB accounts/sites
  can be created.
- OneDrive is **not** suitable for sensitive data, including personal information.

You can move data between OneDrive and CRCD storage graphically
[using Globus](globus-onedrive.md). The rest of this page covers the command-line approaches:
**rclone** is the general-purpose tool, and for folders *shared with you* the
**OneDrive Client for Linux** works better (see below). The
[Globus CLI](https://docs.globus.org/cli/) can also drive transfers against the
`UPitt-OneDrive` collection if you prefer scripting Globus.

## Rclone

[Rclone](https://rclone.org) is a command-line tool for transferring and syncing files between
local storage and OneDrive (and many other services), often faster than `scp` or `rsync`. Load
the CRCD module (run `module spider rclone` to see available versions):

```
module load rclone
```

### Configuring rclone for OneDrive

Configuring OneDrive is hard on a headless machine because it needs a browser for
authentication, so do it from an **HTC Desktop** session on Open OnDemand: log on to
`ondemand.htc.crc.pitt.edu`, launch **Interactive Apps → HTC Desktop** (1 core, 1 hour), then
open a terminal emulator and run:

```
module load rclone
rclone config
```

This creates `~/.config/rclone/rclone.conf`. Answer the prompts:

1. **New remote** → `n`
2. **Name** → something short, e.g. `odrive`
3. **Storage** → type `onedrive` (or the number shown for **Microsoft OneDrive**; the number
   changes between rclone versions, so the name is safer)
4. **client_id / client_secret** → leave blank (press Enter)
5. **region** → `1` (Microsoft Cloud Global); leave **tenant** blank
6. **Advanced config?** → `n`
7. **Use auto config?** → `y` (a browser opens for Pitt Passport authentication)
8. **Connection type** → `1` (OneDrive Personal or Business)
9. Select the listed drive (`0`), confirm, then **quit** with `q`

??? note "Example `rclone config` session"

    ```
    [fangping@htc-n1 ~]$ rclone config
    e/n/d/r/c/s/q> n
    name> odrive
    Storage> onedrive         # the name (or the number shown for Microsoft OneDrive)
    client_id>                # (blank)
    client_secret>            # (blank)
    region> 1                 # Microsoft Cloud Global
    tenant>                   # (blank)
    Edit advanced config? y/n> n
    Use auto config? y/n> y
    # ... browser opens; log in with Pitt Passport and authorize rclone ...
    Your choice> 1            # OneDrive Personal or Business
    Chose drive to use:> 0
    Is that okay? y/n> y
    y/e/d> y
    e/n/d/r/c/s/q> q
    ```

### Rclone usage

The name `odrive` below is whatever you chose during configuration. The trailing colon tells
rclone it's a remote, not a local path.

```
rclone lsd odrive:                          # list top-level folders
rclone ls odrive:                           # list all files
rclone mkdir odrive:backup                  # create a folder
rclone copy <local file> odrive:backup      # copy a file up
rclone copy <local folder> odrive:backup/newfolder   # copy a folder up
rclone sync <local folder> odrive:backup    # full sync (overwrites/deletes at destination!)
```

!!! warning
    `rclone sync` makes the destination match the source — files already in `odrive:backup` may
    be overwritten or deleted. Double-check the destination before syncing.

A few useful options: `--transfers=N` (parallel transfers, default 4),
`--onedrive-chunk-size=SIZE` (upload chunk size; must be a multiple of 320 KiB), and
`--skip-links` (ignore symlinks). OneDrive sends deletions to its recycle bin by default; add
`--onedrive-hard-delete` to remove them permanently. Full list in the
[rclone OneDrive docs](https://rclone.org/onedrive/).

### Automating transfers with a batch job

Scripting rclone lets you schedule periodic backups. Submit a job like:

```
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

rclone has limited support for files *shared with you* on OneDrive
([rclone issue #4062](https://github.com/rclone/rclone/issues/4062)). For shared folders, use the
[OneDrive Client for Linux](https://github.com/abraunegg/onedrive), available as a module:

```
module load onedrive
```

!!! note "Check your client version"
    Shared-folder handling was rewritten in OneDrive Client for Linux **v2.5.0** and is not
    compatible with the older v2.4.x commands (`--synchronize`, `--list-shared-folders`, and the
    `business_shared_folders` file are gone). Run `onedrive --version` to confirm you're on
    v2.5.0 or newer; the steps below use the current workflow.

1. In the OneDrive web interface, open **Shared**, select the folder you want, and click
   **Add shortcut to My files**.
2. Enable shared-item sync by adding this line to `~/.config/onedrive/config`:

    ```
    sync_business_shared_items = "true"
    ```

3. List what's shared with you, then sync. The first run after enabling the option requires a
   `--resync`:

    ```
    onedrive --list-shared-items
    onedrive --sync --download-only --single-directory '<folder>' --verbose
    ```

!!! warning "Shared-item sync can break with Microsoft API changes"
    Microsoft deprecated the Graph `sharedWithMe` API in 2026, which temporarily broke
    shared-item enumeration in the client (fixed around v2.5.11). If `--list-shared-items`
    returns nothing, confirm the CRCD `onedrive` module is recent enough, or download the files
    from the OneDrive web interface instead.

## Sharing files from OneDrive

You can share OneDrive files and folders with anyone and control whether they can edit, comment,
or view — see
[Microsoft's sharing guide](https://support.microsoft.com/en-us/office/share-onedrive-files-and-folders-9fcc2f7d-de0c-4cec-93b0-a82024800c07).
OneDrive's own program policies apply to shared content.

## Related

<div class="grid cards" markdown>

- :material-transit-connection-variant:{ .lg .middle } **OneDrive via Globus**

    ---

    A graphical alternative for moving data to and from OneDrive.

    [:octicons-arrow-right-24: OneDrive via Globus](globus-onedrive.md)

- :material-swap-horizontal:{ .lg .middle } **Other transfer methods**

    ---

    SFTP clients, rsync/scp, Globus, and other cloud tools.

    [:octicons-arrow-right-24: File Transfer Methods](index.md)

</div>
