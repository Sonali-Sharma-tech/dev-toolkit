# Bash One-Liners

Practical one-liners you'll actually use. No fluff, just copy-paste ready commands.

## Table of Contents
- [File Operations](#file-operations)
- [Text Processing](#text-processing)
- [Search & Find](#search--find)
- [System & Process](#system--process)
- [Networking](#networking)
- [Disk & Storage](#disk--storage)
- [Date & Time](#date--time)
- [String Manipulation](#string-manipulation)
- [Loops & Batch Operations](#loops--batch-operations)
- [JSON Processing](#json-processing)
- [Productivity Shortcuts](#productivity-shortcuts)

---

## File Operations

### Find and delete files older than 30 days
```bash
find /path/to/dir -type f -mtime +30 -delete
```
Use `-mtime +30` for modified time, `-atime +30` for access time. Add `-name "*.log"` to target specific files.

### Find large files (>100MB)
```bash
find . -type f -size +100M -exec ls -lh {} \;
```

### Find empty files and directories
```bash
find . -empty                    # Both files and dirs
find . -type f -empty            # Only files
find . -type d -empty            # Only directories
```

### Count files in directory (recursive)
```bash
find . -type f | wc -l
```

### Find duplicate files by content (using md5)
```bash
find . -type f -exec md5 {} \; | sort | uniq -d -w 32
```
On Linux, use `md5sum` instead of `md5`.

### Rename all files to lowercase
```bash
for f in *; do mv "$f" "${f,,}"; done
```

### Bulk rename with pattern (e.g., .txt to .md)
```bash
for f in *.txt; do mv "$f" "${f%.txt}.md"; done
```

### Create backup of file with timestamp
```bash
cp file.txt{,.$(date +%Y%m%d_%H%M%S).bak}
```
Creates `file.txt.20231127_143022.bak`

### Recursively change file permissions
```bash
find . -type f -exec chmod 644 {} \;    # Files to 644
find . -type d -exec chmod 755 {} \;    # Directories to 755
```

### Find files modified in last 24 hours
```bash
find . -type f -mtime -1
```

### Find files modified in last N minutes
```bash
find . -type f -mmin -30            # Last 30 minutes
```

---

## Text Processing

### Remove duplicate lines (preserving order)
```bash
awk '!seen[$0]++' file.txt
```

### Remove duplicate lines (sorted)
```bash
sort file.txt | uniq
```

### Count occurrences of each line
```bash
sort file.txt | uniq -c | sort -rn
```

### Extract unique values from column 2
```bash
awk '{print $2}' file.txt | sort -u
```

### Sum numbers in a file (one per line)
```bash
awk '{sum+=$1} END {print sum}' numbers.txt
```

### Sum specific column (e.g., column 3)
```bash
awk '{sum+=$3} END {print sum}' file.txt
```

### Average of numbers
```bash
awk '{sum+=$1; count++} END {print sum/count}' numbers.txt
```

### Replace text in file (in-place)
```bash
sed -i '' 's/old/new/g' file.txt        # macOS
sed -i 's/old/new/g' file.txt           # Linux
```

### Delete lines matching pattern
```bash
sed -i '' '/pattern/d' file.txt
```

### Delete blank lines
```bash
sed '/^$/d' file.txt
```

### Print lines between two patterns
```bash
sed -n '/START/,/END/p' file.txt
```

### Extract emails from file
```bash
grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' file.txt
```

### Extract URLs from file
```bash
grep -oE 'https?://[^ ]+' file.txt
```

### Extract IP addresses
```bash
grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' file.txt
```

### Remove trailing whitespace
```bash
sed 's/[[:space:]]*$//' file.txt
```

### Convert tabs to spaces
```bash
expand -t 4 file.txt > file_spaces.txt
```

### Show only lines with more than N characters
```bash
awk 'length > 80' file.txt
```

### Reverse file content (line by line)
```bash
tac file.txt                    # Linux
tail -r file.txt                # macOS
```

### Print specific line number
```bash
sed -n '42p' file.txt           # Print line 42
sed -n '10,20p' file.txt        # Print lines 10-20
```

### Print every Nth line
```bash
awk 'NR % 5 == 0' file.txt      # Every 5th line
```

---

## Search & Find

### Find files containing text
```bash
grep -rl "search_term" /path/to/dir
```
`-r` recursive, `-l` list filenames only.

### Find files containing text (with line numbers)
```bash
grep -rn "search_term" /path/to/dir
```

### Find and replace across multiple files
```bash
find . -type f -name "*.js" -exec sed -i '' 's/old/new/g' {} \;
```

### Search excluding directories
```bash
grep -r "pattern" --exclude-dir={node_modules,.git,dist} .
```

### Search specific file types
```bash
grep -r "pattern" --include="*.py" .
```

### Find files by name (case-insensitive)
```bash
find . -iname "*.jpg"
```

### Find recently modified files
```bash
find . -type f -printf '%T+ %p\n' | sort -r | head -20    # Linux
find . -type f -exec stat -f '%m %N' {} \; | sort -rn | head -20    # macOS
```

### Find broken symlinks
```bash
find . -xtype l
```

### Find files NOT matching pattern
```bash
find . -type f ! -name "*.txt"
```

---

## System & Process

### Find which process is using a port
```bash
lsof -i :8080                   # macOS/Linux
netstat -tulpn | grep 8080      # Linux
```

### Kill process on specific port
```bash
kill -9 $(lsof -t -i:8080)
```

### Find process by name
```bash
pgrep -fl "process_name"
ps aux | grep "process_name"
```

### Monitor file changes in real-time
```bash
tail -f /var/log/syslog
tail -f file.log | grep --line-buffered "error"
```

### Watch command output (refresh every 2 seconds)
```bash
watch -n 2 'df -h'
watch -n 1 'kubectl get pods'
```

### List all open files by process
```bash
lsof -p $(pgrep -f "process_name")
```

### Show top 10 memory-consuming processes
```bash
ps aux --sort=-%mem | head -11
```

### Show top 10 CPU-consuming processes
```bash
ps aux --sort=-%cpu | head -11
```

### Get public IP
```bash
curl -s ifconfig.me
curl -s icanhazip.com
curl -s ipinfo.io/ip
```

### Get local IP
```bash
ipconfig getifaddr en0          # macOS (Wi-Fi)
hostname -I | awk '{print $1}'  # Linux
```

### List all environment variables
```bash
printenv | sort
```

### Check if command exists
```bash
command -v git &> /dev/null && echo "git installed" || echo "git not found"
```

### Get exit status of last command
```bash
echo $?
```

### Run command with timeout
```bash
timeout 10 ./long_running_script.sh     # Linux
gtimeout 10 ./script.sh                 # macOS (brew install coreutils)
```

---

## Networking

### Download file
```bash
curl -O https://example.com/file.zip
wget https://example.com/file.zip
```

### Download with custom filename
```bash
curl -o myfile.zip https://example.com/file.zip
```

### Test if port is open
```bash
nc -zv hostname 80
```

### Check HTTP response headers
```bash
curl -I https://example.com
```

### Make POST request with JSON
```bash
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' https://api.example.com
```

### Download and extract in one command
```bash
curl -sL https://example.com/archive.tar.gz | tar xz
```

### Get HTTP status code only
```bash
curl -s -o /dev/null -w "%{http_code}" https://example.com
```

### Test website response time
```bash
curl -s -w "Connect: %{time_connect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\n" -o /dev/null https://example.com
```

### List all listening ports
```bash
lsof -i -P -n | grep LISTEN      # macOS
ss -tulpn                         # Linux
netstat -tulpn                    # Linux (older)
```

### DNS lookup
```bash
dig +short example.com
nslookup example.com
host example.com
```

### Reverse DNS lookup
```bash
dig -x 8.8.8.8 +short
```

### SSH tunnel (local port forwarding)
```bash
ssh -L 8080:localhost:3000 user@remote-server
```
Access remote's localhost:3000 via local localhost:8080

### SSH tunnel (remote port forwarding)
```bash
ssh -R 9090:localhost:3000 user@remote-server
```
Expose local port 3000 on remote's port 9090

### Copy file over SSH
```bash
scp file.txt user@host:/path/to/destination
scp -r folder/ user@host:/path/to/destination    # Recursive
```

### Rsync with progress
```bash
rsync -avz --progress source/ user@host:/destination/
```

---

## Disk & Storage

### Show disk usage (human readable)
```bash
df -h
```

### Show directory size
```bash
du -sh /path/to/dir
du -sh */                        # Size of each subdirectory
```

### Find largest directories
```bash
du -h --max-depth=1 | sort -hr | head -10     # Linux
du -h -d 1 | sort -hr | head -10              # macOS
```

### Find largest files in directory
```bash
find . -type f -exec du -h {} \; | sort -hr | head -20
```

### Clear disk cache (Linux)
```bash
sync; echo 3 > /proc/sys/vm/drop_caches
```

### Show inode usage
```bash
df -i
```

### Create tar.gz archive
```bash
tar -czvf archive.tar.gz folder/
```

### Extract tar.gz
```bash
tar -xzvf archive.tar.gz
```

### Create zip archive
```bash
zip -r archive.zip folder/
```

### Unzip to specific directory
```bash
unzip archive.zip -d /destination/
```

---

## Date & Time

### Current timestamp
```bash
date +%s                         # Unix timestamp
date +%Y-%m-%d                   # 2023-11-27
date +%Y-%m-%d_%H-%M-%S          # 2023-11-27_14-30-22
date +"%Y-%m-%d %H:%M:%S"        # 2023-11-27 14:30:22
```

### Convert timestamp to date
```bash
date -r 1701091822               # macOS
date -d @1701091822              # Linux
```

### Get date N days ago
```bash
date -v-7d +%Y-%m-%d             # macOS (7 days ago)
date -d "7 days ago" +%Y-%m-%d   # Linux
```

### Calculate days between dates
```bash
echo $(( ($(date -d "2024-01-01" +%s) - $(date +%s)) / 86400 )) days
```

### Measure command execution time
```bash
time command_here
```

### Run command at specific time
```bash
echo "command" | at 14:30
```

---

## String Manipulation

### Convert to uppercase
```bash
echo "hello" | tr '[:lower:]' '[:upper:]'
echo "hello" | awk '{print toupper($0)}'
```

### Convert to lowercase
```bash
echo "HELLO" | tr '[:upper:]' '[:lower:]'
```

### Remove specific characters
```bash
echo "hello-world" | tr -d '-'
```

### Generate random string
```bash
openssl rand -hex 16
cat /dev/urandom | LC_ALL=C tr -dc 'a-zA-Z0-9' | head -c 32
```

### Generate UUID
```bash
uuidgen
cat /proc/sys/kernel/random/uuid    # Linux
```

### Base64 encode/decode
```bash
echo "hello" | base64               # Encode
echo "aGVsbG8K" | base64 -d         # Decode (-D on macOS)
```

### URL encode
```bash
python3 -c "import urllib.parse; print(urllib.parse.quote('hello world'))"
```

### Hash string (MD5, SHA256)
```bash
echo -n "hello" | md5               # macOS
echo -n "hello" | md5sum            # Linux
echo -n "hello" | shasum -a 256
```

### Trim whitespace
```bash
echo "  hello  " | xargs
```

### Split string by delimiter
```bash
echo "a,b,c,d" | tr ',' '\n'
```

---

## Loops & Batch Operations

### Loop through files
```bash
for f in *.txt; do echo "Processing $f"; done
```

### Loop through lines in file
```bash
while IFS= read -r line; do echo "$line"; done < file.txt
```

### Loop through command output
```bash
for dir in $(ls -d */); do echo "Directory: $dir"; done
```

### Parallel execution (xargs)
```bash
cat urls.txt | xargs -P 4 -I {} curl -O {}
```
`-P 4` runs 4 parallel processes.

### Run command on each line of file
```bash
cat servers.txt | xargs -I {} ssh {} "uptime"
```

### Loop with counter
```bash
for i in {1..10}; do echo "Iteration $i"; done
```

### Loop through range with step
```bash
for i in $(seq 0 2 10); do echo $i; done    # 0, 2, 4, 6, 8, 10
```

### Infinite loop
```bash
while true; do echo "Running..."; sleep 5; done
```

### Retry command until success
```bash
until command_here; do echo "Retrying..."; sleep 2; done
```

### Process files in batches
```bash
find . -name "*.jpg" | xargs -n 10 -I {} echo "Batch: {}"
```

---

## JSON Processing

### Pretty print JSON
```bash
cat file.json | jq '.'
curl -s https://api.example.com | jq '.'
```

### Extract specific field
```bash
cat file.json | jq '.name'
cat file.json | jq '.users[0].name'
```

### Extract raw string (no quotes)
```bash
cat file.json | jq -r '.name'
```

### Filter array
```bash
cat file.json | jq '.users[] | select(.age > 25)'
```

### Get array length
```bash
cat file.json | jq '.users | length'
```

### Get keys
```bash
cat file.json | jq 'keys'
```

### Transform JSON
```bash
cat file.json | jq '{name: .title, id: .user_id}'
```

### Flatten nested JSON
```bash
cat file.json | jq '[.users[].name]'
```

### Merge JSON files
```bash
jq -s '.[0] * .[1]' file1.json file2.json
```

### CSV to JSON
```bash
cat file.csv | python3 -c "import csv,json,sys; print(json.dumps(list(csv.DictReader(sys.stdin))))"
```

### JSON to CSV
```bash
cat file.json | jq -r '.[] | [.name, .email] | @csv'
```

---

## Productivity Shortcuts

### Create directory and cd into it
```bash
mkcd() { mkdir -p "$1" && cd "$1"; }
```

### Quick HTTP server
```bash
python3 -m http.server 8000
python -m SimpleHTTPServer 8000    # Python 2
```

### Open current directory in Finder/Explorer
```bash
open .                   # macOS
xdg-open .               # Linux
explorer .               # Windows (Git Bash)
```

### Copy file content to clipboard
```bash
pbcopy < file.txt        # macOS
xclip -selection c < file.txt    # Linux
```

### Paste from clipboard to file
```bash
pbpaste > file.txt       # macOS
xclip -selection c -o > file.txt    # Linux
```

### Last command shortcuts
```bash
!!                       # Repeat last command
sudo !!                  # Repeat last command with sudo
!$                       # Last argument of previous command
!^                       # First argument of previous command
!*                       # All arguments of previous command
```

### History shortcuts
```bash
history | grep "pattern"
!123                     # Run command #123 from history
ctrl+r                   # Reverse search history
```

### Quick calculator
```bash
echo $((5 * 10 + 2))
bc <<< "5.5 * 2.3"       # For decimals
```

### Create file with content
```bash
cat > file.txt << 'EOF'
Line 1
Line 2
EOF
```

### Compare files
```bash
diff file1.txt file2.txt
diff -y file1.txt file2.txt     # Side by side
```

### Compare directories
```bash
diff -rq dir1/ dir2/
```

### Clear terminal but keep scrollback
```bash
clear
```

### Clear terminal and scrollback
```bash
printf '\033[2J\033[3J\033[1;1H'    # Works in most terminals
```

### Run command without saving to history
```bash
 command_here            # Note the leading space
```

### Sudo last command
```bash
sudo !!
```

---

## Quick Reference: Useful Aliases

Add these to your `~/.bashrc` or `~/.zshrc`:

```bash
# Navigation
alias ..="cd .."
alias ...="cd ../.."
alias ~="cd ~"

# Listing
alias ll="ls -alh"
alias la="ls -A"
alias l="ls -CF"

# Safety nets
alias rm="rm -i"
alias cp="cp -i"
alias mv="mv -i"

# Git shortcuts
alias gs="git status"
alias gp="git pull"
alias gd="git diff"
alias gl="git log --oneline -10"

# Quick edits
alias hosts="sudo nano /etc/hosts"
alias bashrc="nano ~/.bashrc && source ~/.bashrc"
alias zshrc="nano ~/.zshrc && source ~/.zshrc"

# Utilities
alias myip="curl -s ifconfig.me"
alias ports="lsof -i -P -n | grep LISTEN"
alias weather="curl wttr.in"
alias path='echo $PATH | tr ":" "\n"'

# Docker shortcuts
alias dps="docker ps"
alias dpa="docker ps -a"
alias di="docker images"
alias dex="docker exec -it"
```

---

*Pro tip: Test destructive commands with `echo` first: `echo rm -rf folder/` to see what would be deleted.*
