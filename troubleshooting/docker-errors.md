# Docker Error Messages Decoded

When Docker throws an error, find it here.

## Table of Contents
- [port is already allocated](#port-is-already-allocated)
- [no space left on device](#no-space-left-on-device)
- [Cannot connect to the Docker daemon](#cannot-connect-to-the-docker-daemon)
- [image not found / manifest unknown](#image-not-found--manifest-unknown)
- [container is already running](#container-is-already-running)
- [name is already in use by container](#name-is-already-in-use-by-container)
- [OCI runtime create failed](#oci-runtime-create-failed)
- [permission denied while connecting to Docker](#permission-denied-while-connecting-to-docker)
- [network not found](#network-not-found)
- [Error response from daemon: conflict](#error-response-from-daemon-conflict)
- [exited with code 137](#exited-with-code-137)
- [exited with code 1](#exited-with-code-1)
- [COPY failed: file not found](#copy-failed-file-not-found)

---

## port is already allocated

```
Error response from daemon: driver failed programming external connectivity
Bind for 0.0.0.0:3000 failed: port is already allocated
```

### What it means
Another process is already using that port.

### Quick fix
```bash
# Find what's using the port
lsof -i :3000

# Kill it
kill $(lsof -t -i :3000)

# Or use a different port
docker run -p 3001:3000 myimage
```

### If it's another Docker container
```bash
# Find the container
docker ps | grep 3000

# Stop it
docker stop container-name

# Or see all port bindings
docker ps --format "{{.Names}}: {{.Ports}}"
```

---

## no space left on device

```
Error response from daemon: no space left on device
```

### What it means
Docker has used up all available disk space.

### Quick fix
```bash
# See what's using space
docker system df

# Clean up everything unused
docker system prune -a --volumes -f
```

### Step by step cleanup
```bash
# Remove stopped containers
docker container prune -f

# Remove unused images
docker image prune -a -f

# Remove unused volumes (⚠️ data loss)
docker volume prune -f

# Remove build cache
docker builder prune -f
```

### Reclaim massive space
```bash
# Nuclear option - removes EVERYTHING
docker system prune -a --volumes -f

# Can reclaim 10-100GB depending on usage
```

---

## Cannot connect to the Docker daemon

```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock.
Is the docker daemon running?
```

### What it means
Docker Desktop isn't running, or the daemon crashed.

### Quick fix (macOS/Windows)
```bash
# Start Docker Desktop from Applications
# Or from command line (macOS):
open -a Docker
```

### Quick fix (Linux)
```bash
# Start Docker service
sudo systemctl start docker

# Enable on boot
sudo systemctl enable docker

# Check status
sudo systemctl status docker
```

### If daemon is running but you can't connect
```bash
# Check socket permissions
ls -la /var/run/docker.sock

# Add yourself to docker group (Linux)
sudo usermod -aG docker $USER

# Log out and back in for group change to take effect
```

---

## image not found / manifest unknown

```
Error response from daemon: manifest for myimage:latest not found:
manifest unknown: manifest unknown
```

### What it means
Docker can't find the image you're trying to pull or run.

### Quick fix
```bash
# Check if image name is correct
docker search imagename

# Check available tags
# Go to Docker Hub and look at "Tags" tab

# Pull with specific tag
docker pull nginx:1.25

# Not "latest"
docker pull nginx:alpine
```

### If it's a private registry
```bash
# Login first
docker login registry.example.com

# Then pull
docker pull registry.example.com/image:tag
```

### Common mistakes
```bash
# Wrong: docker pull ubuntu/latest
# Right: docker pull ubuntu:latest

# Wrong: docker pull node@18
# Right: docker pull node:18
```

---

## container is already running

```
Error response from daemon: container already running
```

### What it means
You're trying to start a container that's already started.

### Quick fix
```bash
# See running containers
docker ps

# If you want to restart it
docker restart container-name

# If you want to connect to it
docker exec -it container-name bash
```

---

## name is already in use by container

```
Error response from daemon: Conflict. The container name "/myapp" is already
in use by container "abc123". You have to remove (or rename) that container
to be able to reuse that name.
```

### What it means
A container with that name already exists (running or stopped).

### Quick fix
```bash
# Remove the old container
docker rm myapp

# If it's running, force remove
docker rm -f myapp

# Or use a different name
docker run --name myapp-2 myimage
```

### Use --rm to auto-cleanup
```bash
# Container removes itself when stopped
docker run --rm --name myapp myimage
```

---

## OCI runtime create failed

```
OCI runtime create failed: container_linux.go:349:
starting container process caused "exec: \"node\": executable file not found in $PATH"
```

### What it means
The command you're trying to run doesn't exist in the container.

### Quick fix
```bash
# Check what's in the container
docker run -it myimage sh

# Look at the Dockerfile CMD/ENTRYPOINT
# Make sure the executable exists in the image

# Common fix: use full path
CMD ["/usr/local/bin/node", "app.js"]
```

### If the image is wrong architecture
```bash
# Check image architecture
docker inspect myimage | grep Architecture

# Pull correct architecture
docker pull --platform linux/amd64 myimage
```

---

## permission denied while connecting to Docker

```
Got permission denied while trying to connect to the Docker daemon socket at
unix:///var/run/docker.sock
```

### What it means
Your user doesn't have permission to use Docker.

### Quick fix (Linux)
```bash
# Add user to docker group
sudo usermod -aG docker $USER

# Apply changes (or log out and back in)
newgrp docker

# Verify
docker ps
```

### Temporary fix
```bash
# Change socket permissions (resets on reboot)
sudo chmod 666 /var/run/docker.sock

# Not recommended for production!
```

---

## network not found

```
Error response from daemon: network mynetwork not found
```

### What it means
The Docker network you're trying to use doesn't exist.

### Quick fix
```bash
# Create the network
docker network create mynetwork

# Then run your container
docker run --network mynetwork myimage
```

### List available networks
```bash
docker network ls
```

---

## Error response from daemon: conflict

```
Error response from daemon: conflict: unable to delete abc123 (must be forced)
- image is being used by stopped container xyz789
```

### What it means
You're trying to delete an image that's still in use.

### Quick fix
```bash
# Remove containers using the image first
docker rm xyz789

# Then remove the image
docker rmi abc123

# Or force remove
docker rmi -f abc123
```

### Remove all stopped containers then images
```bash
docker container prune -f
docker image prune -a -f
```

---

## exited with code 137

```
mycontainer exited with code 137
```

### What it means
Container was killed, usually due to Out of Memory (OOM).

### Quick fix
```bash
# Check if it was OOM
docker inspect container-name | grep -A 5 "OOMKilled"

# If true, increase memory limit
docker run -m 2g myimage

# Or in docker-compose.yml:
services:
  app:
    deploy:
      resources:
        limits:
          memory: 2G
```

### Other causes of 137
- Docker was stopped/restarted
- `docker stop` sends SIGTERM then SIGKILL after timeout
- System ran out of memory

---

## exited with code 1

```
mycontainer exited with code 1
```

### What it means
Your application crashed with an error.

### Quick fix
```bash
# Check the logs
docker logs container-name

# The error message is in the logs
docker logs --tail 50 container-name

# Run interactively to debug
docker run -it myimage sh
```

### Common causes
- Application error (bug in code)
- Missing environment variable
- Can't connect to database
- File not found
- Permission denied

---

## COPY failed: file not found

```
COPY failed: file not found in build context or excluded by .dockerignore
```

### What it means
Dockerfile COPY can't find the file you're trying to copy.

### Quick fix
```bash
# Check the file exists
ls path/to/file

# Check you're building from the right context
docker build -t myimage .
#                        ^ this directory

# Check .dockerignore isn't excluding it
cat .dockerignore
```

### Common mistakes
```dockerfile
# Wrong: absolute paths
COPY /Users/me/project/app.js /app/

# Right: relative to build context
COPY app.js /app/

# Wrong: outside build context
COPY ../shared /app/  # Can't go above build context

# Right: include shared in context or use different structure
```

---

## Quick Reference

| Error | Likely Cause | Quick Fix |
|-------|--------------|-----------|
| port already allocated | Port in use | `lsof -i :PORT` then kill |
| no space left | Docker filled disk | `docker system prune -a` |
| cannot connect to daemon | Docker not running | Start Docker Desktop |
| image not found | Typo or wrong tag | Check image name/tag |
| name already in use | Container exists | `docker rm container-name` |
| OCI runtime failed | Bad command | Check CMD in Dockerfile |
| permission denied | Not in docker group | `sudo usermod -aG docker $USER` |
| network not found | Network doesn't exist | `docker network create` |
| conflict (delete) | Image in use | Remove containers first |
| exit code 137 | Out of memory | Increase memory limit |
| exit code 1 | App error | Check `docker logs` |
| COPY failed | File not found | Check path and .dockerignore |

---

## Container Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success (completed normally) |
| 1 | Application error |
| 126 | Permission problem or not executable |
| 127 | Command not found |
| 137 | Killed (OOM or docker stop) |
| 139 | Segmentation fault |
| 143 | Graceful termination (SIGTERM) |

