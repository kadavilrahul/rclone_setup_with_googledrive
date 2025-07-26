# rclone Google Drive Backup Manager

A comprehensive bash script for managing automated backups to Google Drive using rclone. This tool provides an interactive menu system for configuring multiple Google Drive remotes, uploading backups, and restoring files with advanced folder browsing capabilities.

## Quick Start

### Clone and Setup

```bash
# Clone the repository
git clone https://github.com/rahuldineshk/rclone-googledrive-backup.git
cd rclone-googledrive-backup

# Copy and configure your credentials
cp sample_config.json config.json
# Edit config.json with your Google Drive API credentials

# Make the script executable
chmod +x rclone.sh

# Run the setup (requires sudo)
sudo ./rclone.sh
```

### First Time Setup

1. **Install rclone** (Option 1 in main menu)
2. **Configure your Google Drive remote** (Option 4 → Select remote → Option 1)
3. **Start backing up** (Option 4 → Select remote → Option 3)

## Features

- **Interactive Menu System**: Easy-to-use command-line interface
- **Multiple Remote Support**: Configure and manage multiple Google Drive accounts
- **Advanced File Browser**: Navigate remote folders with intuitive controls
- **Batch Operations**: Upload/download multiple files with flexible selection
- **Automated Setup**: One-click rclone installation and configuration
- **Status Monitoring**: Real-time remote accessibility and storage usage
- **Secure Configuration**: JSON-based config with credential management

## Installation

### Prerequisites

- Ubuntu/Debian-based Linux system
- Root/sudo access
- Internet connection for Google Drive authentication
- Web browser for OAuth authentication

### Automatic Installation

The script will automatically install required dependencies:
- `rclone` - Cloud storage sync tool
- `jq` - JSON processor for configuration management

### Manual Installation

```bash
# Install dependencies
sudo apt update
sudo apt install -y rclone jq

# Download and setup
wget <script-url>
chmod +x rclone.sh
```

## Configuration

### Setting up Google Drive API

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project or select existing one
3. Enable Google Drive API
4. Create OAuth 2.0 credentials (Desktop application)
5. Copy Client ID and Client Secret

### Configuration File

Edit `config.json` with your credentials:

```json
{
  "rclone_remotes": [
    {
      "client_id": "your-google-client-id",
      "client_secret": "your-google-client-secret", 
      "remote_name": "your_remote_name"
    }
  ]
}
```

## Usage

### Main Menu Options

```
1) Install rclone Package - Download and install rclone with dependencies
2) Show Installation Status & Overview - Check rclone setup and configuration status  
3) Show Existing Remotes Details - Display configured remotes and accessibility
4) Manage a Website Remote - Configure and use remote storage connections
5) Uninstall rclone Package - Remove rclone and all configurations
0) Exit - Return to main menu
```

### Remote Management Options

```
1) Configure or Re-Configure Remote - Set up Google Drive authentication
2) Check Folder Sizes - View local and remote storage usage
3) Copy Backups to Remote - Upload local backups to Drive folder
4) Restore Backups from Drive - Download backups from Drive to local
0) Back to Main Menu - Return to main rclone menu
```

### File Operations

#### Uploading Backups
- Select files individually or in ranges (e.g., `1 3-5`)
- Choose destination folder or create new ones
- Advanced folder browser for precise placement
- Progress tracking for large files

#### Restoring Files
- Interactive folder navigation
- Multi-file selection with range support
- Automatic local directory creation
- Failed transfer tracking and reporting

### Advanced Features

#### Folder Browser
- Navigate remote directory structure
- Create new folders on-the-fly
- Visual indicators for accessibility
- Breadcrumb navigation

#### Batch Selection
```bash
# Examples of file selection syntax:
1 3-5        # Files 1, 3, 4, 5
all          # All available files
1,3,5        # Files 1, 3, 5
2-4,7        # Files 2, 3, 4, 7
```

## Directory Structure

```
rclone_setup_with_googledrive/
├── rclone.sh              # Main script
├── config.json            # Configuration file (keep private)
├── sample_config.json     # Example configuration
├── README.md              # This file
├── LICENSE                # License information
└── .gitignore            # Git ignore rules
```

## Default Paths

- **Backup Source**: `/website_backups` (configurable in script)
- **Log Directory**: `/var/log`
- **Config File**: `./config.json`
- **rclone Config**: `~/.config/rclone/`

## Troubleshooting

### Common Issues

**Remote not accessible**
```bash
# Check remote configuration
rclone config show remote_name

# Test connection
rclone lsf remote_name: --max-depth 1
```

**Authentication expired**
- Re-run remote configuration (Option 4 → 1)
- Complete browser authentication process

**Permission denied**
- Ensure script is run with sudo
- Check file permissions: `chmod +x rclone.sh`

### Log Files

Check logs for detailed error information:
```bash
# View rclone logs
tail -f /var/log/rclone_remote_name.log

# Check system logs
journalctl -u rclone
```

## Security Considerations

- **Never commit `config.json`** - Contains sensitive API credentials
- Use `.gitignore` to exclude sensitive files
- Regularly rotate Google API credentials
- Monitor remote access logs in Google Cloud Console
- Use dedicated service accounts for production environments

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For issues and questions:
- Check the troubleshooting section above
- Review rclone documentation: https://rclone.org/docs/
- Open an issue in the repository

## Changelog

### v1.0.0
- Initial release
- Interactive menu system
- Multi-remote support
- Advanced folder browser
- Batch file operations
- Comprehensive error handling