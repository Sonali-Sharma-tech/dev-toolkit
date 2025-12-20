# Terminal One-Liners

> 30 one-liners you'll actually use. Each one solves a real problem.

---

## Process Management

### Kill process on port
```bash
lsof -ti:3000 | xargs kill -9
```
**When**: Port already in use, need to free it fast.

### Find what's using a port
```bash
lsof -i :8080
```
**When**: Something's running but you don't know what.

### Kill all processes by name
```bash
pkill -f "node"
```
**When**: Need to stop all instances of an app.

---

## File Search

### Find files by name
```bash
find . -name "*.js" -not -path "*/node_modules/*"
```
**When**: Find files but skip dependencies.

### Find files modified today
```bash
find . -type f -mtime 0
```
**When**: "What did I change today?"

### Find large files (>100MB)
```bash
find . -type f -size +100M -exec ls -lh {} \;
```
**When**: Hunting for disk space hogs.

---

## Text Search

### Search for text in all files
```bash
grep -rn "TODO" --include="*.js" .
```
**When**: Find all occurrences with line numbers.

### Find files containing text
```bash
grep -rl "api_key" .
```
**When**: "Which files have this string?"

### Count occurrences
```bash
grep -rc "console.log" --include="*.js" . | grep -v ":0$"
```
**When**: Audit how often something appears.

---

## Text Manipulation

### Replace text in file
```bash
sed -i '' 's/old/new/g' file.txt        # macOS
sed -i 's/old/new/g' file.txt           # Linux
```
**When**: Quick find-and-replace.

### Replace in multiple files
```bash
find . -name "*.js" -exec sed -i '' 's/foo/bar/g' {} \;
```
**When**: Rename variable across codebase.

### Remove duplicate lines
```bash
sort file.txt | uniq
```
**When**: Dedupe a list.

### Extract column from CSV
```bash
cut -d',' -f2 data.csv
```
**When**: Pull out a single column.

---

## Disk & Cleanup

### Show disk usage by directory
```bash
du -sh */ | sort -hr | head -10
```
**When**: Find what's taking space.

### Delete files older than 30 days
```bash
find /path/to/logs -type f -mtime +30 -delete
```
**When**: Clean up old logs.

### Count files in directory
```bash
find . -type f | wc -l
```
**When**: "How many files are here?"

---

## Network

### Get public IP
```bash
curl -s ifconfig.me
```
**When**: What's my external IP?

### Test if port is open
```bash
nc -zv hostname 443
```
**When**: Check if server is reachable.

### Download file
```bash
curl -O https://example.com/file.zip
```
**When**: Grab a file from URL.

### Quick HTTP server
```bash
python3 -m http.server 8000
```
**When**: Serve files locally for testing.

---

## Git Shortcuts

### Undo last commit (keep changes)
```bash
git reset --soft HEAD~1
```
**When**: Committed too soon.

### Find who changed a line
```bash
git blame -L 10,20 file.js
```
**When**: "Who wrote this?"

### Clean up merged branches
```bash
git branch --merged | grep -v '\*\|main\|master' | xargs git branch -d
```
**When**: Tidy up local branches.

---

## JSON

### Pretty print JSON
```bash
cat file.json | jq '.'
```
**When**: Make JSON readable.

### Extract field from JSON
```bash
cat file.json | jq -r '.data.name'
```
**When**: Pull specific value.

---

## System

### Watch command output
```bash
watch -n 2 'df -h'
```
**When**: Monitor something changing.

### Run command on file change
```bash
fswatch -o file.txt | xargs -n1 -I{} echo "File changed"
```
**When**: Auto-rebuild on save (macOS).

### Repeat until success
```bash
until ping -c1 google.com &>/dev/null; do sleep 1; done; echo "Online!"
```
**When**: Wait for network/service to come up.

---

## Clipboard

### Copy file to clipboard (macOS)
```bash
pbcopy < file.txt
```
**When**: Paste file contents somewhere.

### Copy command output to clipboard
```bash
ls -la | pbcopy
```
**When**: Share output without copying manually.

---

*For more detailed recipes, see [Recipes](recipes/) folder.*
