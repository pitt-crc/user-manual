# Managing Data

This section covers CRCD file systems, data transfer methods, and file management best practices.

## File Systems

CRCD provides multiple storage tiers optimized for different use cases:

| File System | Path | Quota | Backed Up | Purpose |
|-------------|------|-------|-----------|---------|
| Home | `/ihome/<username>` | 75 GB | Yes (snapshots) | Scripts, configurations, small files |
| Project (BGFS) | `/bgfs/<PI>/<username>` | 5 TB shared | No | Research data |
| Project (IX1) | `/ix1/<PI>/<username>` | 5 TB shared | No | Research data |
| Scratch | `/scratch` | Per-job | No | Temporary job data |

For complete details, see [File Systems](file-systems.md).

## Transferring Data

Choose the right tool for your data transfer needs:

| Method | Best For | Guide |
|--------|----------|-------|
| [Globus](file-transfer-methods/globus.md) | Large datasets, reliable transfers | Recommended for large files |
| rsync/scp | Small to medium files from terminal | See below |
| [OneDrive](file-transfer-methods/microsoft-onedrive.md) | Integration with Pitt cloud storage | Limited by OneDrive quotas |
| SFTP clients | GUI-based file management | Cyberduck, FileZilla |
| Open OnDemand | Quick uploads under 1 GB | Web browser |

## Quick Transfer Commands

**Copy files to the cluster:**
```bash
rsync -aP local_files/ username@htc.crc.pitt.edu:/bgfs/PI/username/
```

**Copy files from the cluster:**
```bash
rsync -aP username@htc.crc.pitt.edu:/bgfs/PI/username/data/ ./local_dir/
```

## Additional Topics

- [File Permissions](file-permissions.md) - Managing access control and group permissions
- [Globus with OneDrive](file-transfer-methods/globus-microsoft-onedrive.md) - Connecting Globus to your Pitt OneDrive

!!! warning
    Scratch storage is **not backed up** and files may be automatically purged. Always copy important results to your project directory after jobs complete.

!!! tip
    For transfers larger than 1 GB, use [Globus](file-transfer-methods/globus.md) for reliable, resumable transfers with better performance than scp/rsync.
