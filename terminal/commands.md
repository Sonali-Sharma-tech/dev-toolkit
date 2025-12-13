# Terminal Commands Reference

Core terminal commands for file management, navigation, and system operations.

## Table of Contents
- [Navigation](#navigation)
- [File Operations](#file-operations)
- [Directory Operations](#directory-operations)
- [Viewing Files](#viewing-files)
- [File Permissions](#file-permissions)
- [Search](#search)
- [Text Processing](#text-processing)
- [Compression](#compression)
- [Disk & Storage](#disk--storage)
- [System Information](#system-information)
- [User Management](#user-management)
- [Environment](#environment)
- [Redirection & Pipes](#redirection--pipes)

---

## Navigation

### pwd - Print working directory
```bash
pwd                         # Show current directory path
```

### cd - Change directory
```bash
cd /path/to/dir             # Go to specific directory
cd                          # Go to home directory
cd ~                        # Go to home directory
cd -                        # Go to previous directory
cd ..                       # Go up one level
cd ../..                    # Go up two levels
cd ~/Documents              # Go to Documents in home
```

### ls - List directory contents
```bash
ls                          # Basic list
ls -l                       # Long format (permissions, size, date)
ls -la                      # Long format including hidden files
ls -lh                      # Human-readable sizes (KB, MB, GB)
ls -lt                      # Sort by modification time (newest first)
ls -ltr                     # Sort by time (oldest first)
ls -lS                      # Sort by size (largest first)
ls -R                       # Recursive listing
ls -d */                    # List directories only
ls *.txt                    # List only .txt files
ls -1                       # One file per line
```

### tree - Display directory tree
```bash
tree                        # Show directory structure
tree -L 2                   # Limit to 2 levels deep
tree -d                     # Directories only
tree -a                     # Include hidden files
tree -I 'node_modules|.git' # Ignore patterns
# Install: brew install tree (macOS) or apt install tree (Linux)
```

---

## File Operations

### touch - Create file / Update timestamp
```bash
touch file.txt              # Create empty file or update timestamp
touch -t 202312011200 file  # Set specific timestamp
touch file1.txt file2.txt   # Create multiple files
```

### cp - Copy files
```bash
cp source.txt dest.txt      # Copy file
cp source.txt /path/to/dir/ # Copy to directory
cp -r source_dir/ dest_dir/ # Copy directory recursively
cp -i file.txt dest/        # Interactive (prompt before overwrite)
cp -v file.txt dest/        # Verbose (show what's being copied)
cp -n file.txt dest/        # No clobber (don't overwrite)
cp -p file.txt dest/        # Preserve timestamps and permissions
cp *.txt backup/            # Copy all .txt files
```

### mv - Move or rename files
```bash
mv old.txt new.txt          # Rename file
mv file.txt /path/to/dir/   # Move file to directory
mv file1.txt file2.txt dir/ # Move multiple files
mv -i source dest           # Interactive (prompt before overwrite)
mv -n source dest           # No clobber (don't overwrite)
mv -v source dest           # Verbose
mv *.txt archive/           # Move all .txt files
```

### rm - Remove files
```bash
rm file.txt                 # Delete file
rm -r directory/            # Delete directory and contents
rm -rf directory/           # Force delete (no confirmation)
rm -i file.txt              # Interactive (confirm each file)
rm -v file.txt              # Verbose
rm *.log                    # Delete all .log files

# Safety tip: Use -i or trash command instead of rm
```

### ln - Create links
```bash
ln -s /path/to/original symlink     # Create symbolic link
ln original hardlink                 # Create hard link
ln -sf /path/to/new symlink          # Force overwrite existing symlink
readlink symlink                     # Show link target
```

---

## Directory Operations

### mkdir - Create directories
```bash
mkdir dirname               # Create directory
mkdir -p path/to/deep/dir   # Create nested directories
mkdir -m 755 dirname        # Create with specific permissions
mkdir dir1 dir2 dir3        # Create multiple directories
mkdir -v dirname            # Verbose
```

### rmdir - Remove empty directories
```bash
rmdir dirname               # Remove empty directory
rmdir -p path/to/empty/     # Remove nested empty directories
```

### Common patterns
```bash
# Create and enter directory
mkdir myproject && cd myproject

# Create directory structure
mkdir -p project/{src,test,docs,build}

# Create backup directory with date
mkdir -p backups/$(date +%Y-%m-%d)
```

---

## Viewing Files

### cat - Concatenate and display
```bash
cat file.txt                # Display entire file
cat -n file.txt             # With line numbers
cat -b file.txt             # Number non-blank lines only
cat file1.txt file2.txt     # Display multiple files
cat file1.txt file2.txt > combined.txt  # Concatenate to new file
```

### less - Page through file
```bash
less file.txt               # View file (scrollable)
# Navigation:
# Space/f    - Forward one page
# b          - Back one page
# g          - Go to start
# G          - Go to end
# /pattern   - Search forward
# ?pattern   - Search backward
# n          - Next match
# N          - Previous match
# q          - Quit
```

### head - View beginning of file
```bash
head file.txt               # First 10 lines
head -n 20 file.txt         # First 20 lines
head -c 100 file.txt        # First 100 bytes
head -n -5 file.txt         # All but last 5 lines
```

### tail - View end of file
```bash
tail file.txt               # Last 10 lines
tail -n 20 file.txt         # Last 20 lines
tail -f file.txt            # Follow (live updates)
tail -f -n 50 file.txt      # Follow, starting with last 50 lines
tail -F file.txt            # Follow, retry if file rotates
```

### wc - Word count
```bash
wc file.txt                 # Lines, words, characters
wc -l file.txt              # Lines only
wc -w file.txt              # Words only
wc -c file.txt              # Bytes only
wc -m file.txt              # Characters only
wc -l *.txt                 # Count for multiple files
```

### file - Determine file type
```bash
file document.pdf           # Show file type
file -b document.pdf        # Brief (no filename)
file *                      # Check all files
```

### stat - File statistics
```bash
stat file.txt               # Detailed file info
stat -f "%z" file.txt       # File size (macOS)
stat --printf="%s" file.txt # File size (Linux)
```

---

## File Permissions

### chmod - Change permissions
```bash
# Numeric mode (common)
chmod 755 file              # rwxr-xr-x
chmod 644 file              # rw-r--r--
chmod 600 file              # rw-------
chmod 777 file              # rwxrwxrwx (avoid!)

# Symbolic mode
chmod +x file               # Add execute for all
chmod u+x file              # Add execute for user
chmod g+w file              # Add write for group
chmod o-r file              # Remove read for others
chmod u=rwx,g=rx,o=r file   # Set specific permissions
chmod -R 755 directory/     # Recursive

# Permission reference:
# r=4, w=2, x=1
# 755 = rwxr-xr-x = user:all, group:read+exec, others:read+exec
# 644 = rw-r--r-- = user:read+write, group:read, others:read
```

### chown - Change owner
```bash
chown user file             # Change owner
chown user:group file       # Change owner and group
chown -R user:group dir/    # Recursive
sudo chown root file        # Change to root
```

### chgrp - Change group
```bash
chgrp groupname file        # Change group
chgrp -R groupname dir/     # Recursive
```

---

## Search

### find - Find files
```bash
# By name
find . -name "*.txt"            # Files matching pattern
find . -iname "*.TXT"           # Case-insensitive
find . -name "file*"            # Files starting with "file"

# By type
find . -type f                  # Files only
find . -type d                  # Directories only
find . -type l                  # Symbolic links

# By size
find . -size +100M              # Larger than 100MB
find . -size -1k                # Smaller than 1KB
find . -empty                   # Empty files/directories

# By time
find . -mtime -7                # Modified in last 7 days
find . -mtime +30               # Modified more than 30 days ago
find . -mmin -60                # Modified in last 60 minutes
find . -newer reference.txt    # Newer than reference file

# By permissions
find . -perm 755                # Exact permissions
find . -perm -u+x               # User executable

# Execute commands
find . -name "*.log" -delete              # Delete matching files
find . -name "*.txt" -exec cat {} \;      # Run command on each
find . -name "*.txt" -exec mv {} backup/ \;  # Move all matches
find . -type f -exec chmod 644 {} \;      # Change permissions

# Combine with grep
find . -name "*.js" -exec grep -l "function" {} \;

# Exclude directories
find . -name "*.js" -not -path "*/node_modules/*"
```

### locate - Quick file search
```bash
locate filename             # Fast search (uses database)
sudo updatedb               # Update locate database
locate -i filename          # Case-insensitive
locate -n 10 filename       # Limit to 10 results
# Note: locate is faster than find but may not have recent files
```

### which - Find command location
```bash
which python                # Path to executable
which -a python             # All matching executables in PATH
```

### whereis - Locate binary/source/man
```bash
whereis git                 # Binary, source, man page locations
```

### type - Describe command
```bash
type ls                     # Is it alias, builtin, or file?
type -a ls                  # All locations/definitions
```

---

## Text Processing

### grep - Search text patterns
```bash
grep "pattern" file.txt         # Search for pattern
grep -i "pattern" file.txt      # Case-insensitive
grep -n "pattern" file.txt      # Show line numbers
grep -c "pattern" file.txt      # Count matches
grep -l "pattern" *.txt         # List files with matches
grep -L "pattern" *.txt         # List files without matches
grep -r "pattern" directory/    # Recursive search
grep -v "pattern" file.txt      # Invert (non-matching lines)
grep -w "word" file.txt         # Match whole words only
grep -A 3 "pattern" file.txt    # Show 3 lines after match
grep -B 3 "pattern" file.txt    # Show 3 lines before match
grep -C 3 "pattern" file.txt    # Show 3 lines around match
grep -E "regex" file.txt        # Extended regex (egrep)
grep -o "pattern" file.txt      # Only matching part
```

### sed - Stream editor
```bash
sed 's/old/new/' file.txt           # Replace first occurrence per line
sed 's/old/new/g' file.txt          # Replace all occurrences
sed -i '' 's/old/new/g' file.txt    # In-place edit (macOS)
sed -i 's/old/new/g' file.txt       # In-place edit (Linux)
sed -n '5,10p' file.txt             # Print lines 5-10
sed '/pattern/d' file.txt           # Delete lines matching pattern
sed '5d' file.txt                   # Delete line 5
sed 's/^/prefix/' file.txt          # Add prefix to each line
sed 's/$/suffix/' file.txt          # Add suffix to each line
```

### awk - Pattern processing
```bash
awk '{print $1}' file.txt           # Print first column
awk '{print $1, $3}' file.txt       # Print columns 1 and 3
awk -F',' '{print $1}' file.csv     # Use comma as separator
awk 'NR==5' file.txt                # Print line 5
awk 'NR>=5 && NR<=10' file.txt      # Print lines 5-10
awk '/pattern/' file.txt            # Lines matching pattern
awk '{sum+=$1} END {print sum}'     # Sum first column
awk '{print NR, $0}' file.txt       # Add line numbers
awk 'length > 80' file.txt          # Lines longer than 80 chars
```

### sort - Sort lines
```bash
sort file.txt                   # Alphabetical sort
sort -n file.txt                # Numeric sort
sort -r file.txt                # Reverse sort
sort -u file.txt                # Unique (remove duplicates)
sort -k2 file.txt               # Sort by column 2
sort -t',' -k2 file.csv         # Sort CSV by column 2
sort -h file.txt                # Human-readable numbers (1K, 2M)
```

### uniq - Filter duplicates
```bash
uniq file.txt                   # Remove adjacent duplicates
sort file.txt | uniq            # Remove all duplicates
uniq -c file.txt                # Count occurrences
uniq -d file.txt                # Only show duplicates
uniq -u file.txt                # Only show unique lines
```

### cut - Extract columns
```bash
cut -d',' -f1 file.csv          # First column (comma delimiter)
cut -d':' -f1,3 file.txt        # Columns 1 and 3
cut -c1-10 file.txt             # Characters 1-10
cut -c5- file.txt               # From character 5 to end
```

### tr - Translate characters
```bash
tr 'a-z' 'A-Z' < file.txt       # Lowercase to uppercase
tr -d ' ' < file.txt            # Delete spaces
tr -s ' ' < file.txt            # Squeeze repeated spaces
tr '\n' ',' < file.txt          # Newlines to commas
```

### paste - Merge files
```bash
paste file1.txt file2.txt       # Merge line by line
paste -d',' file1.txt file2.txt # With comma separator
paste -s file.txt               # Merge all lines into one
```

### diff - Compare files
```bash
diff file1.txt file2.txt        # Show differences
diff -u file1.txt file2.txt     # Unified format (patch format)
diff -y file1.txt file2.txt     # Side by side
diff -q file1.txt file2.txt     # Quick (just report if different)
diff -r dir1/ dir2/             # Compare directories
```

---

## Compression

### tar - Archive files
```bash
# Create archives
tar -cvf archive.tar files/         # Create tar archive
tar -czvf archive.tar.gz files/     # Create gzipped tar
tar -cjvf archive.tar.bz2 files/    # Create bzip2 tar
tar -cJvf archive.tar.xz files/     # Create xz tar

# Extract archives
tar -xvf archive.tar                # Extract tar
tar -xzvf archive.tar.gz            # Extract gzipped tar
tar -xjvf archive.tar.bz2           # Extract bzip2 tar
tar -xJvf archive.tar.xz            # Extract xz tar
tar -xzvf archive.tar.gz -C dest/   # Extract to directory

# List contents
tar -tvf archive.tar                # List files in archive

# Flags: c=create, x=extract, t=list, v=verbose, f=file
#        z=gzip, j=bzip2, J=xz
```

### gzip / gunzip
```bash
gzip file.txt                   # Compress (creates file.txt.gz)
gzip -k file.txt                # Keep original file
gzip -d file.txt.gz             # Decompress
gunzip file.txt.gz              # Decompress (same as gzip -d)
gzip -l file.txt.gz             # Show compression info
```

### zip / unzip
```bash
zip archive.zip file1 file2     # Create zip
zip -r archive.zip directory/   # Zip directory recursively
unzip archive.zip               # Extract
unzip archive.zip -d dest/      # Extract to directory
unzip -l archive.zip            # List contents
unzip -o archive.zip            # Overwrite without prompting
```

---

## Disk & Storage

### df - Disk free space
```bash
df                              # All filesystems
df -h                           # Human-readable sizes
df -h /                         # Specific filesystem
df -i                           # Inode usage
```

### du - Disk usage
```bash
du -sh directory/               # Total size of directory
du -sh */                       # Size of each subdirectory
du -h directory/                # Size of directory tree
du -ah directory/               # Include files
du -h --max-depth=1 /           # One level deep (Linux)
du -hd 1 /                      # One level deep (macOS)
du -h | sort -hr | head -10     # Top 10 largest
```

### mount / umount
```bash
mount                           # Show mounted filesystems
sudo mount /dev/sda1 /mnt       # Mount device
sudo umount /mnt                # Unmount
```

---

## System Information

### uname - System info
```bash
uname -a                        # All system info
uname -s                        # Kernel name
uname -r                        # Kernel release
uname -m                        # Machine architecture
```

### hostname
```bash
hostname                        # Show hostname
hostname -f                     # Full hostname (FQDN)
```

### uptime
```bash
uptime                          # System uptime and load
```

### date
```bash
date                            # Current date and time
date +%Y-%m-%d                  # Format: 2024-01-15
date +%s                        # Unix timestamp
date -d @1705305600             # Convert timestamp (Linux)
date -r 1705305600              # Convert timestamp (macOS)
```

### cal
```bash
cal                             # Current month calendar
cal 2024                        # Full year
cal 3 2024                      # March 2024
```

### who / w / whoami
```bash
whoami                          # Current username
who                             # Logged in users
w                               # Who and what they're doing
id                              # User and group IDs
groups                          # Groups current user belongs to
```

### ps - Process status
```bash
ps                              # Current shell processes
ps aux                          # All processes (BSD style)
ps -ef                          # All processes (Unix style)
ps aux | grep process           # Find specific process
ps -p 1234                      # Info about PID 1234
```

### top / htop
```bash
top                             # Interactive process viewer
htop                            # Better process viewer (install: brew install htop)
```

### kill - Terminate processes
```bash
kill 1234                       # Terminate PID 1234 (SIGTERM)
kill -9 1234                    # Force kill (SIGKILL)
kill -l                         # List all signals
killall processname             # Kill by name
pkill -f "pattern"              # Kill by pattern
```

---

## User Management

### sudo - Superuser do
```bash
sudo command                    # Run as root
sudo -u user command            # Run as specific user
sudo su                         # Switch to root shell
sudo -i                         # Root shell (login shell)
sudo !!                         # Repeat last command as root
```

### su - Switch user
```bash
su username                     # Switch to user
su -                            # Switch to root with env
su - username                   # Switch with user's environment
```

---

## Environment

### export - Set environment variables
```bash
export VAR=value                # Set and export variable
export PATH="$PATH:/new/path"   # Add to PATH
unset VAR                       # Remove variable
```

### printenv / env
```bash
printenv                        # All environment variables
printenv PATH                   # Specific variable
env                             # Same as printenv
env VAR=value command           # Run with temporary variable
```

### alias
```bash
alias                           # List all aliases
alias ll='ls -la'               # Create alias
unalias ll                      # Remove alias
```

### source - Execute script in current shell
```bash
source ~/.bashrc                # Reload config
. ~/.bashrc                     # Same as source
```

---

## Redirection & Pipes

### Output redirection
```bash
command > file                  # Redirect stdout (overwrite)
command >> file                 # Redirect stdout (append)
command 2> file                 # Redirect stderr
command 2>> file                # Redirect stderr (append)
command &> file                 # Redirect both stdout and stderr
command > file 2>&1             # Redirect stderr to stdout
command 2>/dev/null             # Discard errors
command &>/dev/null             # Discard all output
```

### Input redirection
```bash
command < file                  # Use file as input
command << EOF                  # Here document
Line 1
Line 2
EOF
command <<< "string"            # Here string
```

### Pipes
```bash
command1 | command2             # Pipe output to next command
command1 | tee file | command2  # Save and pass through
command1 | xargs command2       # Pass as arguments
```

### Command chaining
```bash
command1 && command2            # Run command2 if command1 succeeds
command1 || command2            # Run command2 if command1 fails
command1 ; command2             # Run both regardless
(command1 ; command2)           # Run in subshell
```

### xargs - Build commands from input
```bash
echo file1 file2 | xargs rm     # rm file1 file2
find . -name "*.txt" | xargs cat    # Cat all txt files
find . -name "*.log" | xargs -I {} mv {} backup/  # Move with placeholder
cat list.txt | xargs -P 4 -I {} curl {}  # Parallel execution
```

### tee - Read and write
```bash
command | tee file              # Output to screen AND file
command | tee -a file           # Append to file
command | tee file1 file2       # Multiple files
```

---

## Quick Reference

### Common patterns
```bash
# Find and delete
find . -name "*.tmp" -delete

# Find and execute
find . -name "*.sh" -exec chmod +x {} \;

# Search and replace in files
find . -name "*.txt" -exec sed -i 's/old/new/g' {} \;

# Count files
find . -type f | wc -l

# Disk usage sorted
du -sh */ | sort -hr

# Watch file changes
tail -f /var/log/syslog

# Quick backup
cp file{,.bak}

# Create directory and enter
mkdir dir && cd $_
```

---

*Pro tip: Use `man command` or `command --help` to learn more about any command.*
