# Docker Cleanup Guide

> **Warning**: Docker WILL eat your disk space. This guide helps you reclaim it.

## Table of Contents
- [Quick Cleanup (Safe)](#quick-cleanup-safe)
- [Nuclear Cleanup (Aggressive)](#nuclear-cleanup-aggressive)
- [Understanding What's Using Space](#understanding-whats-using-space)
- [Cleanup by Category](#cleanup-by-category)
- [Automated Cleanup](#automated-cleanup)
- [Prevention Tips](#prevention-tips)

---

## Quick Cleanup (Safe)

### The 5-Command Reclaim

Run these to safely reclaim disk space without affecting running containers:

```bash
# 1. Remove stopped containers
docker container prune -f

# 2. Remove unused images
docker image prune -f

# 3. Remove unused volumes
docker volume prune -f

# 4. Remove unused networks
docker network prune -f

# 5. Remove build cache
docker builder prune -f
```

### One-Liner Version
```bash
docker system prune -f
```

> **Note**: `docker system prune` doesn't remove volumes by default. Add `--volumes` to include them.

---

## Nuclear Cleanup (Aggressive)

### Reclaim Maximum Space

```bash
# Remove EVERYTHING unused (including volumes)
# ⚠️ This will delete all stopped containers, unused images, volumes, and networks
docker system prune -a --volumes -f
```

### What This Removes
| Resource | What's Deleted |
|----------|---------------|
| Containers | All stopped containers |
| Images | All images not used by running containers |
| Volumes | All volumes not used by containers |
| Networks | All networks not used by containers |
| Build cache | All build cache |

### Complete Reset (Start Fresh)
```bash
# Stop all containers
docker stop $(docker ps -q)

# Remove all containers
docker rm -f $(docker ps -aq)

# Remove all images
docker rmi -f $(docker images -q)

# Remove all volumes
docker volume rm $(docker volume ls -q)

# Remove all networks (except default)
docker network rm $(docker network ls -q --filter "type=custom")

# Clean build cache
docker builder prune -af
```

---

## Understanding What's Using Space

### Check Overall Disk Usage
```bash
# Summary
docker system df

# Detailed breakdown
docker system df -v
```

**Sample Output:**
```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          45        3         12.5GB    11.2GB (89%)
Containers      12        2         1.2GB     980MB (81%)
Local Volumes   8         2         5.6GB     4.1GB (73%)
Build Cache     156       0         3.2GB     3.2GB (100%)
```

### Find Large Images
```bash
# Sort images by size
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -hr | head -20

# Find images over 1GB
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | grep -E "^[0-9]+(\.[0-9]+)?GB"
```

### Find Large Volumes
```bash
# List volumes with size (requires inspection)
docker system df -v | grep -A 100 "Local Volumes"

# Check specific volume size
docker run --rm -v myvolume:/data alpine du -sh /data
```

### Find Large Containers
```bash
# Show container sizes
docker ps -as --format "table {{.Size}}\t{{.Names}}\t{{.Image}}"

# Include stopped containers
docker ps -as --format "table {{.Size}}\t{{.Names}}\t{{.Status}}"
```

---

## Cleanup by Category

### Containers

```bash
# Remove all stopped containers
docker container prune -f

# Remove containers older than 24 hours
docker container prune --filter "until=24h" -f

# Remove containers by name pattern
docker rm $(docker ps -aq --filter "name=test")

# Remove exited containers
docker rm $(docker ps -aq -f status=exited)

# Remove containers with specific exit code
docker rm $(docker ps -aq -f exited=1)
```

### Images

```bash
# Remove dangling images (untagged)
docker image prune -f

# Remove ALL unused images (not just dangling)
docker image prune -a -f

# Remove images older than 24 hours
docker image prune -a --filter "until=24h" -f

# Remove images by pattern
docker rmi $(docker images | grep "dev-" | awk '{print $3}')

# Remove all images except specific ones
docker images | grep -v "nginx\|redis\|postgres" | awk 'NR>1 {print $3}' | xargs docker rmi -f
```

### Volumes

```bash
# Remove unused volumes
docker volume prune -f

# Remove specific volume
docker volume rm myvolume

# Remove all volumes (⚠️ data loss!)
docker volume rm $(docker volume ls -q)

# List dangling volumes
docker volume ls -f dangling=true

# Remove dangling volumes only
docker volume rm $(docker volume ls -qf dangling=true)
```

### Networks

```bash
# Remove unused networks
docker network prune -f

# Remove networks older than 24 hours
docker network prune --filter "until=24h" -f

# Remove specific network
docker network rm my-network

# Remove all custom networks
docker network rm $(docker network ls -q --filter "type=custom")
```

### Build Cache

```bash
# Remove all build cache
docker builder prune -f

# Remove all build cache (including intermediate)
docker builder prune -af

# Remove build cache older than 24 hours
docker builder prune --filter "until=24h" -f

# Check build cache size
docker builder du
```

---

## Automated Cleanup

### Cron Job (Daily Cleanup)
```bash
# Add to crontab (runs at 3 AM daily)
# crontab -e
0 3 * * * docker system prune -f >> /var/log/docker-cleanup.log 2>&1
```

### Weekly Aggressive Cleanup
```bash
# Add to crontab (runs Sunday at 4 AM)
0 4 * * 0 docker system prune -a --volumes -f >> /var/log/docker-cleanup.log 2>&1
```

### Cleanup Script
```bash
#!/bin/bash
# save as: /usr/local/bin/docker-cleanup.sh

echo "=== Docker Cleanup $(date) ==="
echo ""

# Show before stats
echo "Before cleanup:"
docker system df
echo ""

# Perform cleanup
echo "Removing stopped containers..."
docker container prune -f

echo "Removing unused images..."
docker image prune -f

echo "Removing unused volumes..."
docker volume prune -f

echo "Removing unused networks..."
docker network prune -f

echo "Removing build cache..."
docker builder prune -f

echo ""
echo "After cleanup:"
docker system df

echo ""
echo "Cleanup complete!"
```

### Docker Compose Cleanup
```bash
# Remove containers, networks, and images for a project
docker-compose down --rmi all --volumes

# Remove just containers and networks
docker-compose down

# Remove orphan containers
docker-compose down --remove-orphans
```

---

## Prevention Tips

### Use --rm Flag
```bash
# Container is automatically removed when stopped
docker run --rm ubuntu echo "I clean up after myself"
```

### Multi-Stage Builds
```dockerfile
# Build stage (large)
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm ci && npm run build

# Production stage (small)
FROM node:18-alpine
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]
```

### .dockerignore
```dockerignore
# Reduce context size (faster builds, smaller images)
node_modules
.git
*.log
.env
dist
coverage
```

### Limit Log Size
```bash
# In docker run
docker run -d --log-opt max-size=10m --log-opt max-file=3 nginx

# In docker-compose.yml
services:
  app:
    logging:
      options:
        max-size: "10m"
        max-file: "3"
```

### Configure Daemon Defaults
```json
// /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Check disk usage | `docker system df` |
| Safe cleanup | `docker system prune -f` |
| Aggressive cleanup | `docker system prune -a --volumes -f` |
| Remove stopped containers | `docker container prune -f` |
| Remove unused images | `docker image prune -a -f` |
| Remove unused volumes | `docker volume prune -f` |
| Remove build cache | `docker builder prune -f` |
| Find large images | `docker images --format "{{.Size}}\t{{.Repository}}" \| sort -hr` |

---

## Space Savings Cheat Sheet

| Action | Typical Space Saved |
|--------|-------------------|
| Remove stopped containers | 100MB - 2GB |
| Remove dangling images | 500MB - 5GB |
| Remove unused images | 5GB - 50GB |
| Remove unused volumes | 1GB - 20GB |
| Clear build cache | 2GB - 10GB |
| **Full prune with volumes** | **10GB - 100GB** |

> **Tip**: Run `docker system prune -a --volumes` monthly if you use Docker heavily. Your disk will thank you.

