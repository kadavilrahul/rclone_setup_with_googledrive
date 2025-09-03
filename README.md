# rclone Google Drive Backup Manager

A comprehensive bash script for managing automated backups to Google Drive using rclone. This tool provides an interactive menu system for configuring multiple Google Drive remotes, uploading backups, and restoring files with advanced folder browsing capabilities.

## Quick Start

### Clone and Setup
# Clone the repository

```bash
git clone https://github.com/rahuldineshk/rclone-googledrive-backup.git
```
```bash
cd rclone-googledrive-backup
```
```
# Copy and configure your credentials
cp sample_config.json config.json
# Edit config.json with your Google Drive API credentials

# Make the script executable
chmod +x run.sh

# Run the setup (requires sudo)
sudo ./run.sh
```

### First Time Setup
1. **Install rclone** (Option 1)
2. **Configure remote** (Option 4)  
3. **Start backing up** (Option 5)

## Menu System
```
1. Install rclone Package           ./run.sh install      # Download and install rclone
2. System Health Check              ./run.sh health       # Check setup and status
3. Show Remote Details              ./run.sh remotes      # Display configured remotes
4. Configure New Remote             ./run.sh config       # Set up Google Drive auth
5. Upload Backups to Drive          ./run.sh upload       # Copy local backups to Drive
6. Download from Drive              ./run.sh download     # Restore backups from Drive
7. Check Storage Usage              ./run.sh sizes        # View storage usage
8. Test Remote Connection           ./run.sh test         # Verify remote access
9. Browse Remote Folders            ./run.sh browse       # Navigate remote directories
10. Sync Directories                ./run.sh sync         # Synchronize directories
11. Remove Remote Configuration     ./run.sh remove       # Delete remote config
12. Complete System Uninstall       ./run.sh uninstall    # Remove everything
0. Exit
```

## Configuration

### Google Drive API Setup
1. [Google Cloud Console](https://console.cloud.google.com/) → Create project → Enable Google Drive API → OAuth 2.0 credentials
2. Copy Client ID and Client Secret to `config.json`:

```json
{
  "rclone_remotes": [
    {
      "client_id": "your-client-id.apps.googleusercontent.com",
      "client_secret": "your-client-secret", 
      "remote_name": "server_backup"
    }
  ]
}
```

## Essential Commands

```bash
# Script operations (interactive or direct)
sudo ./run.sh                      # Interactive menu
sudo ./run.sh install              # Install rclone
sudo ./run.sh config               # Configure remote (select from menu)
sudo ./run.sh config server_backup # Configure specific remote
sudo ./run.sh upload               # Upload backups
sudo ./run.sh download             # Download files
sudo ./run.sh sync                 # Sync directories
sudo ./run.sh help                 # Show all available commands

# Direct rclone commands
rclone ls server_backup:                                # List files
rclone copy /local/path/ server_backup:folder/ --progress  # Upload with progress
rclone copy server_backup:file.txt /local/path/            # Download file
rclone sync /local/ server_backup:backup/ --dry-run        # Sync (test first)
rclone mkdir server_backup:newfolder                       # Create folder
rclone about server_backup:                                # Storage info
```

**Useful Options:** `--progress` `--dry-run` `--include "*.tar.gz"` `--exclude "*.tmp"` `--bwlimit 1M`

## Automation

### Daily Backup Script
```bash
#!/bin/bash
BACKUP_DIR="/home/user/backups"
REMOTE="server_backup:$(date +%Y-%m-%d)"
rclone mkdir "$REMOTE"
rclone copy "$BACKUP_DIR/" "$REMOTE" --progress
```

### Cron Examples
```bash
# Daily backup at 2 AM
0 2 * * * /path/to/daily_backup.sh

# Weekly cleanup
0 3 * * 0 rclone purge server_backup:old_folder
```

## Troubleshooting

### Common Commands
```bash
rclone config show server_backup    # Show config
rclone lsf server_backup: -v        # Test with verbose
rclone version                       # Check version
```

### Common Issues
- **Remote not accessible**: Re-run configuration
- **Permission denied**: Use sudo
- **Auth expired**: Reconfigure remote

## Directory Structure
```
├── run.sh                 # Main script
├── config.json            # Credentials (keep private)
├── sample_config.json     # Example config
└── README.md              # This guide
```

## Security Notes
- Never commit `config.json`
- Use `.gitignore` for sensitive files
- Rotate API credentials regularly
- Test with `--dry-run` first

---
**Always use `--dry-run` for destructive operations like `sync` or `purge`**