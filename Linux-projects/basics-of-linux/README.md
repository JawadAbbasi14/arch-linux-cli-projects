
# Linux CLI Complete Guide

## Table of Contents
1. [Bash Scripting Basics](#bash-scripting-basics)
2. [File Permissions & Users](#file-permissions--users)
3. [Grep and Sed Basics](#grep-and-sed-basics)
4. [Cron Jobs](#cron-jobs)

---

## Bash Scripting Basics

### Introduction
Bash scripting allows you to automate repetitive tasks, create complex workflows, and manage system operations efficiently. Scripts are text files containing sequences of commands that would otherwise be typed manually.

### Basic Script Structure

```bash
#!/bin/bash
# This is a comment - the shebang above tells the system to use bash

# Variables
NAME="John"
DATE=$(date +%Y-%m-%d)

# Basic output
echo "Hello, $NAME! Today is $DATE"
```

### Essential Bash Concepts

#### Variables
```bash
# Declaring variables (no spaces around =)
USERNAME="admin"
COUNT=5
BACKUP_DIR="/home/backups"

# Using variables
echo "User: $USERNAME"
echo "Files to backup: $COUNT"

# Command substitution
CURRENT_TIME=$(date +%H:%M:%S)
FILES_COUNT=`ls -l | wc -l`
```

#### Conditionals
```bash
# If statement
if [ -f "/etc/passwd" ]; then
    echo "Password file exists"
else
    echo "Password file not found"
fi

# Numeric comparison
if [ $COUNT -gt 10 ]; then
    echo "Count is greater than 10"
elif [ $COUNT -eq 10 ]; then
    echo "Count equals 10"
else
    echo "Count is less than 10"
fi

# String comparison
if [ "$USERNAME" = "admin" ]; then
    echo "Admin user detected"
fi
```

#### Loops
```bash
# For loop
for file in *.txt; do
    echo "Processing: $file"
    cp "$file" "$BACKUP_DIR/"
done

# While loop
COUNTER=1
while [ $COUNTER -le 5 ]; do
    echo "Count: $COUNTER"
    COUNTER=$((COUNTER + 1))
done

# Reading file line by line
while IFS= read -r line; do
    echo "Line: $line"
done < input.txt
```

#### Functions
```bash
# Function definition
backup_files() {
    local source_dir=$1
    local dest_dir=$2
    
    echo "Backing up from $source_dir to $dest_dir"
    cp -r "$source_dir"/* "$dest_dir"/
    echo "Backup completed"
}

# Function call
backup_files "/home/data" "/backup/data"
```

### Practical Examples

#### Automated Backup Script
```bash
#!/bin/bash

# Backup script with timestamp
BACKUP_DIR="/backup"
SOURCE_DIR="/home/important"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_$TIMESTAMP.tar.gz"

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR"

# Create compressed backup
tar -czf "$BACKUP_DIR/$BACKUP_NAME" "$SOURCE_DIR"

# Keep only last 7 backups
cd "$BACKUP_DIR"
ls -t backup_*.tar.gz | tail -n +8 | xargs -r rm

echo "Backup completed: $BACKUP_NAME"
```

#### Log Analysis Script
```bash
#!/bin/bash

LOG_FILE="/var/log/syslog"
ERROR_COUNT=$(grep -c "ERROR" "$LOG_FILE")
WARNING_COUNT=$(grep -c "WARNING" "$LOG_FILE")

echo "Log Analysis Report"
echo "==================="
echo "Errors found: $ERROR_COUNT"
echo "Warnings found: $WARNING_COUNT"

# Extract last 10 errors
echo -e "\nLast 10 errors:"
grep "ERROR" "$LOG_FILE" | tail -10
```

---

## File Permissions & Users

### Understanding Linux Permissions

Linux uses a three-tier permission system: **Owner**, **Group**, and **Others**. Each tier has three types of permissions: **Read (r)**, **Write (w)**, and **Execute (x)**.

### Permission Notation

#### Symbolic Notation
```
-rwxr-xr--
│││││││││└─ Others: read only
││││││││└── Others: no write
│││││││└─── Others: no execute
││││││└──── Group: execute
│││││└───── Group: no write
││││└────── Group: read
│││└─────── Owner: execute
││└──────── Owner: write
│└───────── Owner: read
└────────── File type (- for file, d for directory)
```

#### Numeric (Octal) Notation
- Read (r) = 4
- Write (w) = 2
- Execute (x) = 1

Example: `755` means:
- Owner: 7 (4+2+1) = rwx
- Group: 5 (4+0+1) = r-x
- Others: 5 (4+0+1) = r-x

### chmod Command (Change Mode)

#### Using Symbolic Mode
```bash
# Add execute permission for owner
chmod u+x script.sh

# Remove write permission for group
chmod g-w file.txt

# Set exact permissions
chmod u=rwx,g=rx,o=r file.txt

# Add read permission for all
chmod a+r document.pdf

# Multiple changes
chmod u+x,g-w,o-r file.txt
```

#### Using Numeric Mode
```bash
# Common permission sets
chmod 755 script.sh    # rwxr-xr-x (executable)
chmod 644 file.txt     # rw-r--r-- (regular file)
chmod 600 private.key  # rw------- (private file)
chmod 777 shared.txt   # rwxrwxrwx (full access - use carefully!)
chmod 750 program      # rwxr-x--- (group can execute)
```

#### Recursive Permissions
```bash
# Change permissions for directory and all contents
chmod -R 755 /path/to/directory

# Change only directories
find /path -type d -exec chmod 755 {} \;

# Change only files
find /path -type f -exec chmod 644 {} \;
```

### chown Command (Change Owner)

#### Basic Usage
```bash
# Change owner
chown username file.txt

# Change owner and group
chown username:groupname file.txt

# Change only group
chown :groupname file.txt

# Recursive ownership change
chown -R username:groupname /path/to/directory
```

#### Practical Examples
```bash
# Web server files (typical setup)
chown -R www-data:www-data /var/www/html

# User home directory
chown -R john:john /home/john

# Shared project directory
chown -R :developers /opt/project
chmod -R 770 /opt/project
```

### User Management

#### Creating Users
```bash
# Create new user
sudo useradd -m -s /bin/bash newuser

# Create user with specific home directory
sudo useradd -m -d /custom/home -s /bin/bash newuser

# Set password
sudo passwd newuser
```

#### Managing Groups
```bash
# Create group
sudo groupadd developers

# Add user to group
sudo usermod -aG developers username

# View user's groups
groups username

# Remove user from group
sudo gpasswd -d username groupname
```

### Security Best Practices

```bash
# Secure sensitive files
chmod 600 ~/.ssh/id_rsa           # Private SSH key
chmod 644 ~/.ssh/id_rsa.pub       # Public SSH key
chmod 700 ~/.ssh                  # SSH directory

# Web application permissions
find /var/www -type d -exec chmod 755 {} \;
find /var/www -type f -exec chmod 644 {} \;

# Script permissions
chmod 750 /usr/local/bin/backup.sh  # Only owner and group can execute

# Restrict configuration files
chmod 640 /etc/app/config.conf
chown root:appgroup /etc/app/config.conf
```

---

## Grep and Sed Basics

### Grep (Global Regular Expression Print)

Grep is used for searching text patterns in files or output streams.

#### Basic Grep Usage

```bash
# Search for pattern in file
grep "error" logfile.txt

# Case-insensitive search
grep -i "ERROR" logfile.txt

# Search recursively in directory
grep -r "TODO" /path/to/project

# Show line numbers
grep -n "function" script.sh

# Count occurrences
grep -c "failed" system.log

# Invert match (show lines NOT containing pattern)
grep -v "success" results.txt

# Show lines before and after match
grep -B 2 -A 2 "critical" error.log  # 2 lines before and after
grep -C 3 "warning" system.log       # 3 lines context
```

#### Regular Expressions with Grep

```bash
# Beginning of line
grep "^Error" logfile.txt

# End of line
grep "completed$" tasks.txt

# Any single character
grep "s.n" words.txt  # sun, sin, son, etc.

# Character classes
grep "[0-9]" data.txt           # Any digit
grep "[A-Za-z]" text.txt        # Any letter
grep "[^0-9]" mixed.txt         # Not a digit

# Repetition
grep "ab*c" pattern.txt         # a, followed by zero or more b, then c
grep "ab\+c" pattern.txt        # One or more b
grep "ab\?c" pattern.txt        # Zero or one b
grep "a.\{3\}b" text.txt        # Exactly 3 characters between a and b

# Word boundaries
grep "\<error\>" log.txt        # Whole word "error"
grep -w "log" files.txt         # Whole word match
```

#### Practical Grep Examples

```bash
# Find IP addresses
grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" access.log

# Extract email addresses
grep -E "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" contacts.txt

# Find failed SSH attempts
grep "Failed password" /var/log/auth.log | grep -o "[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+"

# Search multiple patterns
grep -E "error|warning|critical" system.log

# Exclude multiple patterns
grep -v -E "debug|info" application.log
```

### Sed (Stream Editor)

Sed is used for text transformation and manipulation.

#### Basic Sed Usage

```bash
# Substitute (replace) text
sed 's/old/new/' file.txt           # Replace first occurrence per line
sed 's/old/new/g' file.txt          # Replace all occurrences
sed 's/old/new/2' file.txt          # Replace second occurrence

# Case-insensitive substitution
sed 's/error/ERROR/gi' log.txt

# Delete lines
sed '5d' file.txt                   # Delete line 5
sed '2,5d' file.txt                  # Delete lines 2-5
sed '$d' file.txt                    # Delete last line
sed '/pattern/d' file.txt           # Delete lines containing pattern

# Print specific lines
sed -n '5p' file.txt                 # Print only line 5
sed -n '2,5p' file.txt               # Print lines 2-5
sed -n '/pattern/p' file.txt        # Print lines with pattern
```

#### In-place Editing

```bash
# Edit file directly (create backup)
sed -i.bak 's/old/new/g' file.txt

# Edit without backup (use carefully!)
sed -i 's/old/new/g' file.txt

# Multiple substitutions
sed -i -e 's/old1/new1/g' -e 's/old2/new2/g' file.txt
```

#### Advanced Sed Operations

```bash
# Insert and append text
sed '3i\This is inserted before line 3' file.txt
sed '3a\This is appended after line 3' file.txt

# Change entire line
sed '5c\This is the new line 5' file.txt

# Using regex groups
sed 's/\([0-9]\+\)-\([0-9]\+\)/\2-\1/g' dates.txt  # Swap number pairs

# Multiple commands
sed -e 's/old/new/g' -e '/^$/d' file.txt  # Replace and delete empty lines

# Address ranges with patterns
sed '/START/,/END/d' file.txt  # Delete from START to END pattern
```

#### Practical Sed Examples

```bash
# Clean up log files
sed 's/\[[0-9]*\]//g' app.log  # Remove numbers in brackets

# Format phone numbers
sed 's/\([0-9]\{3\}\)\([0-9]\{3\}\)\([0-9]\{4\}\)/(\1) \2-\3/' phones.txt

# Remove HTML tags
sed 's/<[^>]*>//g' webpage.html

# Comment out lines in config file
sed 's/^/#/' config.conf  # Add # at beginning
sed '/^option/s/^/#/' config.conf  # Comment lines starting with 'option'

# Extract data between markers
sed -n '/BEGIN/,/END/p' data.txt

# Replace paths in scripts
sed 's|/old/path|/new/path|g' script.sh  # Using | as delimiter
```

### Combining Grep and Sed

```bash
# Find and replace in specific files
grep -l "oldtext" *.txt | xargs sed -i 's/oldtext/newtext/g'

# Process log entries
grep "ERROR" system.log | sed 's/ERROR/CRITICAL/g' > critical.log

# Extract and format data
grep "user:" data.txt | sed 's/user: \(.*\)/Username: \1/'

# Clean and filter logs
cat access.log | grep -v "bot" | sed 's/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+/xxx.xxx.xxx.xxx/g'
```

---

## Cron Jobs

### Introduction to Cron

Cron is a time-based job scheduler in Linux that runs commands or scripts automatically at specified intervals. The cron daemon (crond) runs in the background and checks for scheduled tasks every minute.

### Crontab Syntax

```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 7) (0 or 7 is Sunday)
│ │ │ │ │
│ │ │ │ │
* * * * * command_to_execute
```

### Special Characters

- `*` - Any value
- `,` - Value list separator
- `-` - Range of values
- `/` - Step values
- `@` - Special strings (predefined schedules)

### Managing Crontab

```bash
# Edit current user's crontab
crontab -e

# List current user's cron jobs
crontab -l

# Remove current user's crontab
crontab -r

# Edit another user's crontab (requires sudo)
sudo crontab -u username -e

# List another user's cron jobs
sudo crontab -u username -l
```

### Common Cron Schedule Examples

```bash
# Every minute
* * * * * /path/to/script.sh

# Every 5 minutes
*/5 * * * * /path/to/script.sh

# Every hour at minute 0
0 * * * * /path/to/script.sh

# Every day at 2:30 AM
30 2 * * * /path/to/script.sh

# Every Monday at 3:00 PM
0 15 * * 1 /path/to/script.sh

# First day of every month at midnight
0 0 1 * * /path/to/script.sh

# Every weekday (Mon-Fri) at 8:30 AM
30 8 * * 1-5 /path/to/script.sh

# Every Sunday at 11:00 PM
0 23 * * 0 /path/to/script.sh

# Twice a day (8 AM and 8 PM)
0 8,20 * * * /path/to/script.sh

# Every 15 minutes during business hours
*/15 9-17 * * 1-5 /path/to/script.sh
```

### Special Schedule Strings

```bash
# Equivalent to 0 0 * * *
@daily /path/to/script.sh
@midnight /path/to/script.sh

# Equivalent to 0 * * * *
@hourly /path/to/script.sh

# Equivalent to 0 0 * * 0
@weekly /path/to/script.sh

# Equivalent to 0 0 1 * *
@monthly /path/to/script.sh

# Equivalent to 0 0 1 1 *
@yearly /path/to/script.sh
@annually /path/to/script.sh

# Run at system startup
@reboot /path/to/script.sh
```

### Practical Cron Job Examples

#### Automated Backup
```bash
# Daily backup at 2 AM
0 2 * * * /usr/local/bin/backup.sh

# Backup script content
#!/bin/bash
BACKUP_DIR="/backup"
SOURCE="/important/data"
DATE=$(date +\%Y\%m\%d)
tar -czf $BACKUP_DIR/backup_$DATE.tar.gz $SOURCE
find $BACKUP_DIR -name "backup_*.tar.gz" -mtime +7 -delete
```

#### Log Rotation and Cleanup
```bash
# Rotate logs every Sunday at midnight
0 0 * * 0 /usr/local/bin/rotate_logs.sh

# Clean temp files every hour
0 * * * * find /tmp -type f -mtime +1 -delete

# Compress old logs daily
30 3 * * * find /var/log -name "*.log" -mtime +7 -exec gzip {} \;
```

#### System Monitoring
```bash
# Check disk space every 30 minutes
*/30 * * * * /usr/local/bin/check_disk.sh

# Monitor script
#!/bin/bash
USAGE=$(df -h / | awk 'NR==2 {print $(NF-1)}' | sed 's/%//')
if [ $USAGE -gt 80 ]; then
    echo "Disk usage critical: $USAGE%" | mail -s "Disk Alert" admin@example.com
fi
```

#### Database Maintenance
```bash
# Daily database backup at 1 AM
0 1 * * * mysqldump -u root -ppassword database > /backup/db_$(date +\%Y\%m\%d).sql

# Weekly database optimization
0 3 * * 0 mysqlcheck -u root -ppassword --optimize --all-databases

# Monthly full backup
0 2 1 * * mysqldump -u root -ppassword --all-databases > /backup/full_$(date +\%Y\%m).sql
```

#### Report Generation
```bash
# Generate daily report at 6 AM
0 6 * * * /usr/local/bin/daily_report.sh

# Weekly summary every Monday at 8 AM
0 8 * * 1 /usr/local/bin/weekly_summary.sh

# Monthly statistics on the first day at 9 AM
0 9 1 * * /usr/local/bin/monthly_stats.sh
```

### Cron Environment Variables

```bash
# Set PATH for cron jobs
PATH=/usr/local/bin:/usr/bin:/bin

# Set shell
SHELL=/bin/bash

# Set email for notifications
MAILTO=admin@example.com

# Set home directory
HOME=/home/username

# Example crontab with environment variables
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=admin@example.com

# Cron jobs
0 2 * * * /scripts/backup.sh
*/5 * * * * /scripts/monitor.sh
```

### Debugging Cron Jobs

#### Common Issues and Solutions

```bash
# 1. Redirect output for debugging
* * * * * /path/to/script.sh >> /var/log/cronlog 2>&1

# 2. Use absolute paths
0 * * * * /usr/bin/python3 /home/user/script.py

# 3. Set environment variables in script
#!/bin/bash
export PATH=/usr/local/bin:$PATH
export HOME=/home/username

# 4. Test cron environment
* * * * * env > /tmp/cron_env.txt

# 5. Check cron logs
sudo grep CRON /var/log/syslog
sudo tail -f /var/log/cron
```

#### Cron Job Template with Error Handling

```bash
#!/bin/bash
# Cron job template with logging and error handling

# Set up logging
LOG_FILE="/var/log/cronjob.log"
ERROR_FILE="/var/log/cronjob_error.log"

# Function to log messages
log_message() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" >> "$LOG_FILE"
}

# Start logging
log_message "Starting cron job"

# Main job with error handling
{
    # Your commands here
    /path/to/command
    
    if [ $? -eq 0 ]; then
        log_message "Job completed successfully"
    else
        log_message "Job failed with error code $?"
        exit 1
    fi
} 2>> "$ERROR_FILE"

# End logging
log_message "Cron job finished"
```

### System Cron Directories

```bash
# System-wide cron directories
/etc/cron.hourly/   # Scripts run every hour
/etc/cron.daily/    # Scripts run daily
/etc/cron.weekly/   # Scripts run weekly
/etc/cron.monthly/  # Scripts run monthly

# System crontab file
/etc/crontab

# User crontabs location
/var/spool/cron/crontabs/
```

### Best Practices for Cron Jobs

1. **Always use absolute paths** for commands and files
2. **Redirect output** to log files for debugging
3. **Set necessary environment variables** in the crontab or script
4. **Test scripts manually** before scheduling
5. **Include error handling** in your scripts
6. **Use lock files** to prevent overlapping runs
7. **Document your cron jobs** with comments
8. **Monitor cron job execution** through logs
9. **Use appropriate time zones** if needed
10. **Keep scripts simple and modular**

#### Example: Preventing Overlapping Runs

```bash
#!/bin/bash
# Prevent multiple instances using lock file

LOCKFILE="/var/run/myscript.lock"

# Check if lock file exists
if [ -e "$LOCKFILE" ]; then
    echo "Script is already running"
    exit 1
fi

# Create lock file
touch "$LOCKFILE"

# Ensure lock file is removed on exit
trap "rm -f $LOCKFILE" EXIT

# Your script logic here
echo "Performing task..."
sleep 60

# Lock file will be automatically removed
```

---

## Quick Reference

### Essential Commands Cheat Sheet

```bash
# Bash Scripting
bash script.sh              # Run script
chmod +x script.sh          # Make executable
./script.sh                 # Execute script
source script.sh            # Run in current shell
bash -x script.sh           # Debug mode

# File Permissions
ls -la                      # List with permissions
chmod 755 file              # Change permissions
chown user:group file       # Change ownership
umask 022                   # Set default permissions

# Text Processing
grep "pattern" file         # Search for pattern
grep -r "pattern" dir/      # Recursive search
sed 's/old/new/g' file      # Replace text
sed -i.bak 's/old/new/g'    # In-place edit
awk '{print $1}' file       # Print first column

# Cron Management
crontab -e                  # Edit crontab
crontab -l                  # List cron jobs
service cron status         # Check cron service
tail -f /var/log/syslog     # Monitor cron logs
```

### Useful One-Liners

```bash
# Find and replace in multiple files
find . -type f -name "*.txt" -exec sed -i 's/old/new/g' {} +

# Backup with timestamp
tar -czf backup_$(date +%Y%m%d_%H%M%S).tar.gz /path/to/files

# Monitor log file in real-time
tail -f /var/log/syslog | grep --line-buffered "ERROR"

# Find large files
find / -type f -size +100M -exec ls -lh {} \;

# Count lines in all files
find . -name "*.txt" -exec wc -l {} +

# Remove old files
find /tmp -type f -mtime +30 -delete

# Check disk usage
df -h | awk '$5 > 80 {print $0}'
```

---

## Resources and Further Reading

- **Bash Manual**: `man bash`
- **Chmod Manual**: `man chmod`
- **Grep Manual**: `man grep`
- **Sed Manual**: `man sed`
- **Crontab Manual**: `man 5 crontab`

### Online Resources
- GNU Bash Reference Manual
- Advanced Bash-Scripting Guide
- Regular Expressions Reference
- Crontab Guru (for testing cron expressions)

---

*This guide covers the essential Linux CLI tools and techniques for automation, text processing, and system administration. Practice these commands and concepts regularly to become proficient in Linux system management.*
