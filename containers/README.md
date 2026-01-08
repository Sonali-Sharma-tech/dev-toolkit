# Containers

> Docker and container management.

## Guides

| Guide | What's Inside |
|-------|---------------|
| [Docker Cleanup](docker-cleanup.md) | Reclaim disk space |
| [Docker Compose](docker-compose.md) | Multi-container apps |
| [Docker Reference](docker.md) | Complete command reference |

---

## Quick Commands

```bash
# See what's running
docker ps

# See all containers
docker ps -a

# Stop everything
docker stop $(docker ps -q)

# Clean up everything
docker system prune -a
```

---

## Common Tasks

| Task | Command |
|------|---------|
| Run container | `docker run -it <image>` |
| Stop container | `docker stop <id>` |
| View logs | `docker logs -f <id>` |
| Shell into container | `docker exec -it <id> bash` |
| List images | `docker images` |
| Remove image | `docker rmi <image>` |
