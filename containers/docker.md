# Docker Commands Reference

Essential Docker commands for container management.

## Table of Contents
- [docker run](#docker-run)

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
