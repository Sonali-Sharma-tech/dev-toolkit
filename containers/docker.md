# Docker Commands Reference

Essential Docker commands for container management.

## Table of Contents
- [docker run](#docker-run)
- [docker ps](#docker-ps)
- [docker stop / start](#docker-stop--start)
- [docker rm](#docker-rm)
- [docker images / rmi](#docker-images--rmi)

---

## docker run

Run a container from an image.

```bash
# Basic run (pulls image if not present, runs and exits)
docker run hello-world

# Run interactively with terminal (-it)
docker run -it ubuntu bash

# Run in background (detached mode)
docker run -d nginx

# Run with custom name
docker run -d --name my-nginx nginx

# Run with port mapping (host:container)
docker run -d -p 8080:80 nginx

# Run with multiple ports
docker run -d -p 8080:80 -p 443:443 nginx

# Run with environment variables
docker run -d -e MYSQL_ROOT_PASSWORD=secret mysql

# Run with multiple env vars
docker run -d -e DB_HOST=localhost -e DB_PORT=5432 postgres

# Run with env file
docker run -d --env-file .env myapp

# Run with volume mount (host:container)
docker run -d -v /host/path:/container/path nginx

# Run with named volume
docker run -d -v mydata:/var/lib/mysql mysql

# Run with read-only volume
docker run -d -v /host/path:/container/path:ro nginx

# Run with automatic removal on exit
docker run --rm ubuntu echo "Hello"

# Run with memory limit
docker run -d --memory="512m" nginx

# Run with CPU limit
docker run -d --cpus="1.5" nginx

# Run with restart policy
docker run -d --restart=always nginx
docker run -d --restart=unless-stopped nginx
docker run -d --restart=on-failure:3 myapp

# Run with specific network
docker run -d --network=my-network nginx

# Run with hostname
docker run -d --hostname=myhost nginx

# Run as specific user
docker run -d --user 1000:1000 nginx

# Run with working directory
docker run -d -w /app node npm start

# Combine common flags
docker run -d \
  --name my-app \
  -p 3000:3000 \
  -v $(pwd):/app \
  -e NODE_ENV=production \
  --restart=unless-stopped \
  node:18
```

### Common Flags Reference

| Flag | Description |
|------|-------------|
| `-d` | Detached mode (background) |
| `-it` | Interactive with TTY |
| `--name` | Container name |
| `-p` | Port mapping host:container |
| `-v` | Volume mount |
| `-e` | Environment variable |
| `--env-file` | Load env from file |
| `--rm` | Remove container on exit |
| `--network` | Connect to network |
| `--restart` | Restart policy |
| `--memory` | Memory limit |
| `--cpus` | CPU limit |
| `-w` | Working directory |
| `--user` | Run as user |

---

## docker ps

List containers.

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List only container IDs
docker ps -q

# List all container IDs (including stopped)
docker ps -aq

# Show latest created container
docker ps -l

# Show last N containers
docker ps -n 5

# Show container sizes
docker ps -s

# Filter by status
docker ps -f status=running
docker ps -f status=exited
docker ps -f status=paused

# Filter by name
docker ps -f name=my-container

# Filter by ancestor (image)
docker ps -f ancestor=nginx

# Filter by label
docker ps -f label=env=production

# Filter exited with specific code
docker ps -a -f exited=0
docker ps -a -f exited=1

# Custom format output
docker ps --format "{{.ID}}: {{.Names}}"
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"

# Show only names
docker ps --format "{{.Names}}"

# JSON output
docker ps --format json

# Combine filters
docker ps -a -f status=exited -f ancestor=ubuntu
```

### Format Placeholders

| Placeholder | Description |
|-------------|-------------|
| `.ID` | Container ID |
| `.Image` | Image name |
| `.Command` | Command |
| `.CreatedAt` | Creation time |
| `.Status` | Container status |
| `.Ports` | Port mappings |
| `.Names` | Container names |
| `.Size` | Container size |
| `.Labels` | All labels |
| `.Mounts` | Volume mounts |
| `.Networks` | Network names |

---

## docker stop / start

Stop and start containers.

### docker stop
```bash
# Stop a running container (graceful, SIGTERM then SIGKILL after 10s)
docker stop my-container

# Stop with custom timeout (seconds)
docker stop -t 30 my-container

# Stop multiple containers
docker stop container1 container2 container3

# Stop all running containers
docker stop $(docker ps -q)
```

### docker start
```bash
# Start a stopped container
docker start my-container

# Start with attached output
docker start -a my-container

# Start interactively
docker start -ai my-container

# Start multiple containers
docker start container1 container2 container3
```

### docker restart
```bash
# Restart a container
docker restart my-container

# Restart with timeout
docker restart -t 10 my-container

# Restart all running containers
docker restart $(docker ps -q)
```

### docker pause / unpause
```bash
# Pause a running container (freezes processes)
docker pause my-container

# Unpause a paused container
docker unpause my-container

# Pause all running containers
docker pause $(docker ps -q)
```

---

## docker rm

Remove containers.

```bash
# Remove a stopped container
docker rm my-container

# Remove multiple containers
docker rm container1 container2 container3

# Force remove a running container
docker rm -f my-container

# Remove container and its volumes
docker rm -v my-container

# Remove all stopped containers
docker rm $(docker ps -aq -f status=exited)

# Remove all containers (force)
docker rm -f $(docker ps -aq)

# Remove containers older than 24 hours
docker container prune --filter "until=24h"

# Interactive removal of stopped containers
docker container prune

# Remove containers by filter
docker rm $(docker ps -aq -f ancestor=nginx)
docker rm $(docker ps -aq -f name=test)
```

### Common Patterns

```bash
# Stop and remove all containers
docker stop $(docker ps -q) && docker rm $(docker ps -aq)

# Remove all stopped containers (clean way)
docker container prune -f

# Remove containers with specific label
docker rm $(docker ps -aq -f label=env=test)
```

---

## docker images / rmi

List and remove images.

### docker images
```bash
# List all images
docker images

# List all images (including intermediate)
docker images -a

# List only image IDs
docker images -q

# List with specific repository
docker images nginx

# List with specific tag
docker images nginx:latest

# List dangling images (untagged)
docker images -f dangling=true

# List images by label
docker images -f label=maintainer=me

# List images created before another
docker images -f before=nginx:latest

# List images created since another
docker images -f since=ubuntu:20.04

# Custom format output
docker images --format "{{.Repository}}:{{.Tag}} - {{.Size}}"

# Show digests
docker images --digests

# Sort by size (largest first)
docker images --format "{{.Size}}\t{{.Repository}}:{{.Tag}}" | sort -hr
```

### docker rmi
```bash
# Remove an image
docker rmi nginx

# Remove image by ID
docker rmi abc123def456

# Remove multiple images
docker rmi nginx redis postgres

# Force remove (even if used by containers)
docker rmi -f nginx

# Remove all dangling images
docker rmi $(docker images -f dangling=true -q)

# Remove all images
docker rmi $(docker images -q)

# Remove images by pattern
docker rmi $(docker images | grep "test" | awk '{print $3}')

# Remove unused images (interactive)
docker image prune

# Remove all unused images (not just dangling)
docker image prune -a

# Remove images older than 24 hours
docker image prune -a --filter "until=24h"

# Force prune without confirmation
docker image prune -af
```

### Image Format Placeholders

| Placeholder | Description |
|-------------|-------------|
| `.ID` | Image ID |
| `.Repository` | Repository name |
| `.Tag` | Image tag |
| `.Digest` | Image digest |
| `.CreatedAt` | Creation time |
| `.CreatedSince` | Time since created |
| `.Size` | Image size |

---
