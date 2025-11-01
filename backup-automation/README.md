# Automated Incremental Backup System

A robust Linux shell script that automates the backup process for files modified within the last 24 hours.

## 📋 Overview

This script implements an intelligent backup system that:
- Automatically identifies files modified in the last 24 hours
- Creates compressed archives of only the changed files (incremental backup)
- Organizes backups with timestamp-based naming
- Can be scheduled to run automatically using cron jobs

## 🎯 Features

- **Smart File Detection**: Only backs up files modified in the last 24 hours
- **Incremental Backups**: Saves storage space by backing up only changed files
- **Timestamp Naming**: Each backup is named with a Unix timestamp for easy identification
- **Validation**: Robust input validation ensures both directories exist before proceeding
- **Compressed Archives**: Uses tar.gz compression to minimize backup file size
- **Automation Ready**: Designed to work seamlessly with cron for scheduled backups

## 🛠️ Technologies

- **Shell**: Bash
- **Compression**: tar + gzip
- **Automation**: cron

## 📥 Installation

```bash
# Make the script executable
chmod +x backup.sh
```

## 💻 Usage

### Basic Syntax
```bash
./backup.sh <source_directory> <destination_directory>
```

### Example
```bash
./backup.sh ~/Documents ~/Backups
```

This will:
1. Scan all files in `~/Documents`
2. Identify files modified in the last 24 hours
3. Create a compressed archive (e.g., `backup-1730487654.tar.gz`)
4. Save the archive to `~/Backups`

### Automated Daily Backups

```bash
# Copy script to system location
sudo cp backup.sh /usr/local/bin/

# Add to crontab for daily backups at midnight
crontab -e

# Add this line:
0 0 * * * /usr/local/bin/backup.sh /home/user/documents /home/user/backups
```

## 🔧 How It Works

The script performs the following operations:

1. **Validates** that exactly 2 arguments are provided
2. **Checks** if both directories exist
3. **Gets** current timestamp in epoch format
4. **Generates** unique backup filename with timestamp
5. **Navigates** to the source directory
6. **Calculates** timestamp for 24 hours ago (86,400 seconds)
7. **Loops** through all files in the directory
8. **Compares** each file's modification time with the 24-hour threshold
9. **Collects** qualifying files into an array
10. **Creates** compressed tar.gz archive
11. **Moves** the backup to the destination directory

## 📊 Key Concepts Learned

### Variables and Command Substitution
```bash
targetDirectory=$1
currentTS=$(date +%s)
```

### Timestamp Calculations
```bash
yesterdayTS=$((currentTS - 24*60*60))
```

### File Modification Time Checking
```bash
if (( $(date -r $file +%s) > $yesterdayTS ))
```

### Array Operations
```bash
declare -a toBackup
toBackup+=($file)
```

### Archive Creation
```bash
tar -czf $backupFileName ${toBackup[@]}
```

## 📁 Project Files

- `backup.sh` - Main backup script
- `important-documents/` - Test directory with sample files
- `important-documents.zip` - Test data archive
- `backup-*.tar.gz` - Generated backup archives

## 🔮 Future Enhancements

- Add logging functionality
- Implement email notifications
- Add retention policy (auto-delete old backups)
- Support for exclusion patterns
- Restore functionality
- Cloud storage integration

## 📚 Skills Demonstrated

- Bash scripting
- File system operations
- Date/time manipulation
- Array handling
- Archive creation and compression
- Process automation with cron
- Input validation and error handling

## 👤 Author

**Muhammad Abdullah**
- Course: IBM's "Hands-on Introduction to Linux Commands and Shell Scripting"
- Project: Final Capstone Assignment

## 📄 License

MIT License - Feel free to use and modify for your needs.

---

*Part of IBM Skills Network - Linux Commands and Shell Scripting Course*
