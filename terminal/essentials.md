# Terminal Essentials

> 50 commands you'll use daily. Master these first.

---

## Navigation

```bash
pwd                         # Where am I?
cd /path/to/dir             # Go somewhere
cd                          # Go home
cd -                        # Go back to previous directory
cd ..                       # Go up one level

ls                          # What's here?
ls -la                      # Detailed list with hidden files
ls -lh                      # Human-readable file sizes
ls -lt                      # Sort by time (newest first)
```

---

## Files

### View files

```bash
cat file.txt                # Print entire file
less file.txt               # Scrollable view (q to quit)
head -20 file.txt           # First 20 lines
tail -20 file.txt           # Last 20 lines
tail -f file.txt            # Follow file (live updates)
```

### Create and edit

```bash
touch file.txt              # Create empty file
nano file.txt               # Simple editor (Ctrl+X to exit)
vim file.txt                # Power editor (:q! to exit)
code file.txt               # VS Code
```

### Copy, move, delete

```bash
cp source.txt dest.txt      # Copy file
cp -r source/ dest/         # Copy directory
mv old.txt new.txt          # Rename/move
rm file.txt                 # Delete file (careful!)
rm -rf directory/           # Delete directory (very careful!)
```

---

## Directories

```bash
mkdir newdir                # Create directory
mkdir -p path/to/deep/dir   # Create nested directories
rmdir emptydir              # Remove empty directory
tree -L 2                   # Visual directory tree (2 levels)
```

---

## Search

### Find files

```bash
find . -name "*.js"                    # Find by name
find . -name "*.log" -mtime +7         # Files older than 7 days
find . -type f -size +100M             # Files larger than 100MB
```

### Find text in files

```bash
grep "pattern" file.txt                # Search in file
grep -r "pattern" .                    # Search recursively
grep -rn "pattern" .                   # With line numbers
grep -ri "pattern" .                   # Case-insensitive
grep -l "pattern" *.js                 # List matching files only
```

---

## Permissions

```bash
chmod +x script.sh          # Make executable
chmod 755 file              # rwxr-xr-x (common for scripts)
chmod 644 file              # rw-r--r-- (common for files)
chown user:group file       # Change owner
```

**Permission numbers**: r=4, w=2, x=1. Add them up per user/group/other.

---

## Processes

```bash
ps aux                      # All running processes
ps aux | grep node          # Find specific process
top                         # Interactive process viewer (q to quit)
htop                        # Better process viewer (install first)

kill 1234                   # Graceful kill (PID)
kill -9 1234                # Force kill
pkill -f "node server"      # Kill by name pattern
```

---

## System Info

```bash
df -h                       # Disk space
du -sh directory/           # Directory size
free -h                     # Memory usage (Linux)
uptime                      # System uptime + load
uname -a                    # System info
```

---

## Network

```bash
curl https://example.com              # Fetch URL
curl -I https://example.com           # Headers only
curl -X POST -d '{}' https://api.com  # POST request
wget https://example.com/file.zip     # Download file

ping google.com             # Test connectivity
ssh user@host               # Remote login
scp file.txt user@host:/path/  # Copy to remote
```

---

## Archives

```bash
# Create
tar -czvf archive.tar.gz folder/      # Compress folder
zip -r archive.zip folder/            # Create zip

# Extract
tar -xzvf archive.tar.gz              # Extract tar.gz
unzip archive.zip                     # Extract zip
```

---

## Text Processing

```bash
wc -l file.txt                        # Count lines
sort file.txt                         # Sort lines
uniq file.txt                         # Remove duplicates (sorted input)
sort file.txt | uniq -c               # Count occurrences

awk '{print $1}' file.txt             # First column
cut -d',' -f1 file.csv                # First column (CSV)
sed 's/old/new/g' file.txt            # Replace text
```

---

## Pipes & Redirection

```bash
# Pipes: connect commands
ls -la | grep ".txt"                  # Filter output
cat log.txt | sort | uniq -c | sort -rn  # Chain processing

# Redirection
command > file.txt                    # Output to file (overwrite)
command >> file.txt                   # Output to file (append)
command 2>&1                          # Redirect errors to output
command > /dev/null 2>&1              # Silence all output
```

---

## History & Shortcuts

```bash
history                     # Show command history
history | grep pattern      # Search history
!!                          # Repeat last command
sudo !!                     # Repeat last command with sudo
!$                          # Last argument of previous command
```

### Keyboard shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl + C` | Kill current process |
| `Ctrl + Z` | Suspend process |
| `Ctrl + D` | Exit shell |
| `Ctrl + R` | Search history |
| `Ctrl + L` | Clear screen |
| `Ctrl + A` | Beginning of line |
| `Ctrl + E` | End of line |
| `Ctrl + W` | Delete word back |
| `Tab` | Autocomplete |

---

## Environment

```bash
echo $PATH                  # Show PATH
export VAR=value            # Set environment variable
printenv                    # List all environment variables
source ~/.zshrc             # Reload shell config
which python                # Find command location
```

---

## Job Control

```bash
command &                   # Run in background
jobs                        # List background jobs
fg                          # Bring to foreground
bg                          # Continue in background
Ctrl + Z                    # Suspend current process
```

---

## Quick Patterns

```bash
# Create and enter directory
mkdir project && cd project

# Backup file before editing
cp config.json{,.bak}

# Run command for each file
for f in *.txt; do echo "$f"; done

# Check if command exists
command -v git && echo "installed"
```

---

*Master these and you'll handle 90% of terminal tasks. For specific problems, see [Recipes](recipes/).*
