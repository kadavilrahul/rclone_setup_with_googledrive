# rclone Useful Commands Reference

This document contains useful rclone commands for managing your Google Drive backups outside of the interactive script.

## Prerequisites

Ensure rclone is installed and your remote is configured:
```bash
# Check if rclone is installed
which rclone

# List configured remotes
rclone listremotes

# Test remote connection
rclone lsf server_backup: --max-depth 1
```

## Basic File Operations

### List Files and Directories

```bash
# List all files in root directory
rclone ls server_backup:

# List directories only
rclone lsd server_backup:

# List with file sizes and modification times
rclone lsl server_backup:

# List files in a specific folder
rclone ls server_backup:Backups/

# List with tree structure (limited depth)
rclone tree server_backup: --max-depth 3
```

### Copy Operations

```bash
# Copy a single file to remote
rclone copy /home/rahuldineshk/backups/backup.tar.gz server_backup:

# Copy entire local directory to remote
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/

# Copy with progress display
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --progress

# Copy with verbose output
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ -v

# Copy only newer files (sync-like behavior)
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --update
```

### Download Operations

```bash
# Download a single file
rclone copy server_backup:backup.tar.gz /home/rahuldineshk/backups/

# Download entire directory
rclone copy server_backup:Backups/ /home/rahuldineshk/backups/

# Download with progress
rclone copy server_backup:Backups/ /home/rahuldineshk/backups/ --progress

# Download only if local file doesn't exist
rclone copy server_backup:Backups/ /home/rahuldineshk/backups/ --ignore-existing
```

## Sync Operations

```bash
# Sync local to remote (make remote match local)
rclone sync /home/rahuldineshk/backups/ server_backup:Backups/

# Sync remote to local (make local match remote)
rclone sync server_backup:Backups/ /home/rahuldineshk/backups/

# Dry run sync (see what would be changed)
rclone sync /home/rahuldineshk/backups/ server_backup:Backups/ --dry-run

# Sync with progress and verbose output
rclone sync /home/rahuldineshk/backups/ server_backup:Backups/ --progress -v
```

## Directory Management

```bash
# Create directory
rclone mkdir server_backup:NewFolder

# Remove empty directory
rclone rmdir server_backup:EmptyFolder

# Remove directory and all contents (DANGEROUS!)
rclone purge server_backup:FolderToDelete

# Move/rename directory
rclone moveto server_backup:OldName server_backup:NewName
```

## File Management

```bash
# Delete a specific file
rclone delete server_backup:filename.txt

# Move a file
rclone moveto server_backup:oldname.txt server_backup:newname.txt

# Check if file exists
rclone lsf server_backup: | grep "filename.txt"

# Get file info
rclone lsl server_backup: | grep "filename.txt"
```

## Storage Information

```bash
# Check remote storage usage
rclone about server_backup:

# Get size of specific directory
rclone size server_backup:Backups/

# Count files in directory
rclone lsf server_backup:Backups/ | wc -l

# Check quota and usage details
rclone about server_backup: --json
```

## Advanced Operations

### Filtering

```bash
# Copy only specific file types
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --include "*.tar.gz"

# Exclude specific file types
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --exclude "*.tmp"

# Copy files larger than 100MB
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --min-size 100M

# Copy files modified in last 7 days
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --max-age 7d
```

### Bandwidth Control

```bash
# Limit bandwidth to 1MB/s
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --bwlimit 1M

# Limit bandwidth during business hours
rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --bwlimit "08:00,512k 19:00,10M"
```

### Checksums and Verification

```bash
# Check files for corruption
rclone check /home/rahuldineshk/backups/ server_backup:Backups/

# Generate checksums
rclone md5sum server_backup:Backups/

# Compare checksums between local and remote
rclone checksum md5 /home/rahuldineshk/backups/ server_backup:Backups/
```

## Backup Automation Scripts

### Daily Backup Script

```bash
#!/bin/bash
# daily_backup.sh

BACKUP_DIR="/home/rahuldineshk/backups"
REMOTE="server_backup:Backups/$(date +%Y-%m-%d)"
LOG_FILE="/var/log/rclone_daily_backup.log"

echo "$(date): Starting daily backup" >> "$LOG_FILE"

# Create dated folder and copy backups
rclone mkdir "$REMOTE"
rclone copy "$BACKUP_DIR/" "$REMOTE" --progress --log-file="$LOG_FILE"

echo "$(date): Daily backup completed" >> "$LOG_FILE"
```

### Cleanup Old Backups

```bash
#!/bin/bash
# cleanup_old_backups.sh

# Remove backup folders older than 30 days
rclone lsf server_backup:Backups/ --dirs-only | while read -r folder; do
    folder_date=$(echo "$folder" | grep -E '[0-9]{4}-[0-9]{2}-[0-9]{2}')
    if [ -n "$folder_date" ]; then
        if [ $(date -d "$folder_date" +%s) -lt $(date -d "30 days ago" +%s) ]; then
            echo "Removing old backup: $folder"
            rclone purge "server_backup:Backups/$folder"
        fi
    fi
done
```

## Troubleshooting Commands

```bash
# Test configuration
rclone config show server_backup

# Check connectivity with verbose output
rclone lsf server_backup: -v

# Debug connection issues
rclone lsf server_backup: --log-level DEBUG

# Check rclone version
rclone version

# Update rclone
sudo rclone selfupdate
```

## Useful Aliases

Add these to your `~/.bashrc` for quick access:

```bash
# rclone aliases
alias rcls='rclone ls server_backup:'
alias rclsd='rclone lsd server_backup:'
alias rcup='rclone copy /home/rahuldineshk/backups/ server_backup:Backups/ --progress'
alias rcdown='rclone copy server_backup:Backups/ /home/rahuldineshk/backups/ --progress'
alias rcsize='rclone size server_backup:'
alias rcabout='rclone about server_backup:'
```

## Environment Variables

```bash
# Set default remote
export RCLONE_CONFIG_REMOTE=server_backup

# Set default log level
export RCLONE_LOG_LEVEL=INFO

# Set default progress display
export RCLONE_PROGRESS=true
```

## Cron Job Examples

```bash
# Edit crontab
crontab -e

# Daily backup at 2 AM
0 2 * * * /home/rahuldineshk/rclone_setup_with_googledrive/daily_backup.sh

# Weekly cleanup on Sundays at 3 AM
0 3 * * 0 /home/rahuldineshk/rclone_setup_with_googledrive/cleanup_old_backups.sh

# Hourly sync of critical files
0 * * * * rclone copy /home/rahuldineshk/critical/ server_backup:Critical/ --update
```

## Performance Tips

1. **Use `--fast-list`** for large directories
2. **Set `--transfers`** to control parallel uploads (default: 4)
3. **Use `--checkers`** to control parallel checksum operations
4. **Enable `--use-mmap`** for better memory usage with large files
5. **Use `--buffer-size`** to optimize transfer buffer

```bash
# Optimized large file transfer
rclone copy /large/files/ server_backup:Large/ \
  --transfers 8 \
  --checkers 16 \
  --buffer-size 256M \
  --use-mmap \
  --fast-list \
  --progress
```

---

**Note**: Always test commands with `--dry-run` first when performing destructive operations like `sync`, `delete`, or `purge`.