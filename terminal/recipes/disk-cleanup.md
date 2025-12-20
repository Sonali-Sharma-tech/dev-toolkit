# Recipe: Disk Cleanup

> Find what's eating your disk space and free it up.

---

## Check Disk Usage

### Overview

```bash
# All mounted filesystems
df -h

# Specific directory
df -h /

# Just the percentage used
df -h | awk 'NR==1 || /\/$/'
```

### Directory sizes

```bash
# Size of current directory
du -sh .

# Size of each subdirectory
du -sh */

# Size of each subdirectory, sorted
du -sh */ | sort -hr

# Top 10 largest directories
du -sh */ | sort -hr | head -10

# Depth-limited (avoid going too deep)
du -h -d 1 /        # macOS
du -h --max-depth=1 /   # Linux
```

---

## Find Large Files

### By size

```bash
# Files larger than 100MB
find . -type f -size +100M -exec ls -lh {} \;

# Files larger than 1GB
find . -type f -size +1G -exec ls -lh {} \;

# Sorted by size
find . -type f -size +10M -exec ls -s {} \; | sort -rn | head -20
```

### Interactive tools

```bash
# ncdu - visual disk usage (install: brew install ncdu)
ncdu /

# Navigate: arrows, Enter to drill down, d to delete, q to quit
```

---

## Find Old Files

```bash
# Files not modified in 30+ days
find . -type f -mtime +30

# Files not accessed in 90+ days
find . -type f -atime +90

# Old log files
find /var/log -name "*.log" -mtime +7

# Old downloads
find ~/Downloads -mtime +30 -type f
```

---

## Common Space Hogs

### Development

```bash
# node_modules - often the biggest culprit
find . -name "node_modules" -type d -exec du -sh {} \; 2>/dev/null

# All node_modules size
find . -name "node_modules" -type d -exec du -sh {} \; 2>/dev/null | awk '{sum+=$1} END {print sum "GB total"}'

# Cache directories
du -sh ~/.npm
du -sh ~/.cache
du -sh ~/Library/Caches  # macOS

# Build artifacts
find . -name "dist" -o -name "build" -o -name ".next" | xargs du -sh 2>/dev/null
```

### System (macOS)

```bash
# Xcode derived data
du -sh ~/Library/Developer/Xcode/DerivedData

# iOS device backups
du -sh ~/Library/Application\ Support/MobileSync/Backup

# Docker
docker system df

# Homebrew cache
du -sh ~/Library/Caches/Homebrew
```

### System (Linux)

```bash
# Package cache
du -sh /var/cache/apt/archives    # Debian/Ubuntu
du -sh /var/cache/dnf             # Fedora

# Journal logs
journalctl --disk-usage

# Trash
du -sh ~/.local/share/Trash
```

---

## Safe Cleanup

### Development cleanup

```bash
# Remove node_modules from old projects
# Preview first:
find ~/projects -name "node_modules" -type d -mtime +30

# Then delete:
find ~/projects -name "node_modules" -type d -mtime +30 -exec rm -rf {} +

# Clean npm cache
npm cache clean --force

# Clean yarn cache
yarn cache clean

# Clean pnpm cache
pnpm store prune
```

### Log files

```bash
# Compress old logs
find /var/log -name "*.log" -mtime +7 -exec gzip {} \;

# Delete old compressed logs
find /var/log -name "*.gz" -mtime +30 -delete

# Truncate (empty) large log files
truncate -s 0 /path/to/large.log
```

### Docker cleanup

```bash
# Show space usage
docker system df

# Remove unused containers, networks, images
docker system prune

# Also remove unused volumes (careful!)
docker system prune -a --volumes

# Remove dangling images only
docker image prune

# Remove stopped containers
docker container prune

# Remove unused volumes
docker volume prune
```

### macOS specific

```bash
# Empty trash
rm -rf ~/.Trash/*

# Clear Xcode derived data
rm -rf ~/Library/Developer/Xcode/DerivedData/*

# Clear Homebrew cache
brew cleanup

# Remove old iOS backups (careful - check what you're deleting)
# ls ~/Library/Application\ Support/MobileSync/Backup

# Clear system caches (safe)
rm -rf ~/Library/Caches/*
```

### Linux specific

```bash
# Clean package cache
sudo apt autoremove           # Debian/Ubuntu
sudo apt clean
sudo dnf autoremove           # Fedora
sudo dnf clean all

# Clear journal logs (keep last 7 days)
sudo journalctl --vacuum-time=7d

# Clear thumbnail cache
rm -rf ~/.cache/thumbnails/*
```

---

## Automated Cleanup Script

```bash
#!/bin/bash
# cleanup.sh - Run periodically

echo "=== Disk Usage Before ==="
df -h /

echo ""
echo "=== Cleaning npm cache ==="
npm cache clean --force 2>/dev/null

echo ""
echo "=== Cleaning Homebrew ==="
brew cleanup 2>/dev/null

echo ""
echo "=== Cleaning Docker ==="
docker system prune -f 2>/dev/null

echo ""
echo "=== Cleaning old downloads ==="
find ~/Downloads -mtime +30 -type f -delete 2>/dev/null

echo ""
echo "=== Disk Usage After ==="
df -h /
```

---

## Quick Wins

| Action | Typical Savings |
|--------|-----------------|
| `docker system prune -a` | 5-50 GB |
| Clear node_modules from old projects | 1-10 GB |
| `brew cleanup` | 1-5 GB |
| Clear Xcode DerivedData | 5-20 GB |
| Empty Downloads folder | 1-10 GB |
| Clear browser cache | 1-5 GB |

---

## Monitor Going Forward

```bash
# Check what's growing
# Run this weekly and compare

du -sh ~/* 2>/dev/null | sort -hr | head -10

# Set up alert (add to crontab)
# 0 9 * * 1 df -h / | mail -s "Disk Usage Report" you@email.com
```

---

*Before deleting, always check what you're removing: `ls` first, `rm` second.*
