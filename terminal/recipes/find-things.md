# Recipe: Find Things

> "I need to find..." - files, text, processes, anything.

---

## Find Files

### By name

```bash
# Find all JavaScript files
find . -name "*.js"

# Case-insensitive
find . -iname "*.jpg"

# Exclude directories
find . -name "*.js" -not -path "*/node_modules/*"

# Multiple exclusions
find . -name "*.ts" -not -path "*/node_modules/*" -not -path "*/.git/*"
```

### By type

```bash
# Only files
find . -type f -name "*.md"

# Only directories
find . -type d -name "test*"

# Only symlinks
find . -type l
```

### By size

```bash
# Larger than 100MB
find . -type f -size +100M

# Smaller than 1KB
find . -type f -size -1k

# Between sizes
find . -type f -size +10M -size -100M

# Empty files
find . -type f -empty
```

### By time

```bash
# Modified in last 24 hours
find . -type f -mtime 0

# Modified in last 7 days
find . -type f -mtime -7

# Modified more than 30 days ago
find . -type f -mtime +30

# Modified in last hour
find . -type f -mmin -60

# Accessed today
find . -type f -atime 0
```

### Combined

```bash
# JavaScript files modified this week, excluding deps
find . -name "*.js" -mtime -7 -not -path "*/node_modules/*"

# Large log files older than 7 days
find /var/log -name "*.log" -size +10M -mtime +7
```

---

## Find Text in Files

### Basic grep

```bash
# Search in specific file
grep "pattern" file.txt

# Search recursively
grep -r "pattern" .

# With line numbers
grep -rn "pattern" .

# Case-insensitive
grep -ri "pattern" .
```

### Filter results

```bash
# Only filenames (not content)
grep -rl "TODO" .

# Only in certain file types
grep -r "pattern" --include="*.js" .

# Exclude directories
grep -r "pattern" --exclude-dir={node_modules,.git,dist} .

# Exclude files
grep -r "pattern" --exclude="*.min.js" .
```

### Context

```bash
# Show 3 lines after match
grep -rn -A 3 "function main" .

# Show 3 lines before match
grep -rn -B 3 "error" .

# Show 3 lines around match
grep -rn -C 3 "TODO" .
```

### Advanced patterns

```bash
# Multiple patterns (OR)
grep -E "error|warning|fatal" log.txt

# Whole word match
grep -w "test" file.txt

# Invert (lines NOT matching)
grep -v "debug" log.txt

# Count matches per file
grep -rc "TODO" --include="*.js" . | grep -v ":0$"
```

---

## Find Processes

### By name

```bash
# Find process by name
ps aux | grep "node"

# Better: use pgrep
pgrep -fl "node"

# Find with full details
ps aux | grep "[n]ode"  # Brackets exclude grep itself
```

### By port

```bash
# What's using port 3000?
lsof -i :3000

# Just the PID
lsof -ti :3000

# All listening ports
lsof -i -P -n | grep LISTEN      # macOS
ss -tulpn                         # Linux
```

### By resource

```bash
# Top memory consumers
ps aux --sort=-%mem | head -10

# Top CPU consumers
ps aux --sort=-%cpu | head -10

# Interactive view
htop  # Better than top
```

---

## Find Commands

### Find where a command lives

```bash
# Full path to executable
which python

# All matching executables
which -a python

# What type (alias, function, builtin)?
type python

# Binary, source, and man page
whereis python
```

### Find in history

```bash
# Search command history
history | grep "docker"

# Interactive search
Ctrl+R  # Then type pattern

# Last command starting with...
!docker  # Runs last command starting with "docker"
```

---

## Find Recent Changes

### Recently modified files

```bash
# Modified in last hour
find . -type f -mmin -60

# Sort by modification time
ls -lt | head -20

# Find most recently modified in tree
find . -type f -printf '%T+ %p\n' | sort -r | head -10  # Linux
find . -type f -exec stat -f '%m %N' {} \; | sort -rn | head -10  # macOS
```

### What changed between two times

```bash
# Files modified between dates (using touch reference files)
touch -t 202401010000 /tmp/start
touch -t 202401072359 /tmp/end
find . -newer /tmp/start -not -newer /tmp/end
```

---

## Common Patterns

### Find and act

```bash
# Find and delete
find . -name "*.log" -delete

# Find and list with details
find . -name "*.js" -exec ls -lh {} \;

# Find and move
find . -name "*.bak" -exec mv {} backup/ \;

# Find and run command
find . -name "*.sh" -exec chmod +x {} \;
```

### Locate (faster, uses index)

```bash
# Fast file search (uses pre-built index)
locate filename

# Update the index (run periodically)
sudo updatedb  # Linux

# Case-insensitive
locate -i filename
```

> **Note**: `locate` is faster than `find` for name searches but won't find recently created files until the index updates.

---

*For text manipulation after finding, see [Text Manipulation](text-manipulation.md).*
