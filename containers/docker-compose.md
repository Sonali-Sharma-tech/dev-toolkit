# Docker Compose Guide

Multi-container applications made easy.

## Table of Contents
- [Quick Start](#quick-start)
- [Essential Commands](#essential-commands)
- [Compose File Basics](#compose-file-basics)
- [Common Patterns](#common-patterns)
- [Networking](#networking)
- [Volumes and Data](#volumes-and-data)
- [Environment Variables](#environment-variables)
- [Production Tips](#production-tips)
- [Troubleshooting Scenarios](#troubleshooting-scenarios)

---

## Quick Start

### Minimal docker-compose.yml
```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80"
```

### Start and Stop
```bash
# Start services (foreground)
docker-compose up

# Start services (background)
docker-compose up -d

# Stop services
docker-compose down
```

---

## Essential Commands

### Starting Services
```bash
# Start all services
docker-compose up -d

# Start specific service
docker-compose up -d web

# Start with build
docker-compose up -d --build

# Start with fresh containers
docker-compose up -d --force-recreate

# Start with scale
docker-compose up -d --scale web=3
```

### Stopping Services
```bash
# Stop services (keep containers)
docker-compose stop

# Stop and remove containers
docker-compose down

# Stop, remove containers AND volumes
docker-compose down -v

# Stop, remove containers AND images
docker-compose down --rmi all

# Stop specific service
docker-compose stop web
```

### Viewing Status
```bash
# List running services
docker-compose ps

# List all services (including stopped)
docker-compose ps -a

# View logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Logs for specific service
docker-compose logs -f web

# Last 100 lines
docker-compose logs --tail 100
```

### Managing Services
```bash
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart web

# Rebuild and restart
docker-compose up -d --build web

# Execute command in service
docker-compose exec web bash

# Run one-off command
docker-compose run --rm web npm test
```

### Building
```bash
# Build all images
docker-compose build

# Build specific service
docker-compose build web

# Build without cache
docker-compose build --no-cache

# Build with build args
docker-compose build --build-arg NODE_ENV=production
```

---

## Compose File Basics

### Full Example
```yaml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
      - redis
    volumes:
      - ./src:/app/src
    networks:
      - app-network

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: secret
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - app-network

  redis:
    image: redis:alpine
    networks:
      - app-network

volumes:
  postgres-data:

networks:
  app-network:
```

### Service Options Reference

```yaml
services:
  myservice:
    # Image or Build
    image: nginx:latest
    build:
      context: .
      dockerfile: Dockerfile.prod
      args:
        NODE_ENV: production

    # Container name (optional)
    container_name: my-nginx

    # Port mapping
    ports:
      - "8080:80"           # host:container
      - "443:443"

    # Environment
    environment:
      - NODE_ENV=production
      - API_KEY=${API_KEY}  # From .env or shell
    env_file:
      - .env
      - .env.local

    # Volumes
    volumes:
      - ./code:/app              # Bind mount
      - data:/app/data           # Named volume
      - /app/node_modules        # Anonymous volume

    # Dependencies
    depends_on:
      - db
      - redis

    # Networking
    networks:
      - frontend
      - backend
    expose:
      - "3000"  # Internal only

    # Restart policy
    restart: unless-stopped
    # Options: no, always, on-failure, unless-stopped

    # Resource limits
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 512M

    # Health check
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3

    # Command override
    command: npm start
    entrypoint: /docker-entrypoint.sh

    # Working directory
    working_dir: /app

    # User
    user: "1000:1000"
```

---

## Common Patterns

### Node.js + MongoDB
```yaml
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - MONGODB_URI=mongodb://db:27017/myapp
    depends_on:
      - db
    volumes:
      - .:/app
      - /app/node_modules

  db:
    image: mongo:6
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:
```

### React + API + PostgreSQL
```yaml
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - api

  api:
    build: ./backend
    ports:
      - "4000:4000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/myapp
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    volumes:
      - pg-data:/var/lib/postgresql/data

volumes:
  pg-data:
```

### WordPress + MySQL
```yaml
services:
  wordpress:
    image: wordpress:latest
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wp
      WORDPRESS_DB_PASSWORD: secret
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db
    volumes:
      - wp-content:/var/www/html/wp-content

  db:
    image: mysql:8
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wp
      MYSQL_PASSWORD: secret
      MYSQL_ROOT_PASSWORD: rootsecret
    volumes:
      - db-data:/var/lib/mysql

volumes:
  wp-content:
  db-data:
```

### Nginx Reverse Proxy
```yaml
services:
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./certs:/etc/nginx/certs:ro
    depends_on:
      - app1
      - app2

  app1:
    build: ./app1
    expose:
      - "3000"

  app2:
    build: ./app2
    expose:
      - "3000"
```

---

## Networking

### Default Networking
```yaml
# Services can reach each other by service name
services:
  web:
    image: nginx
    # Can reach api at http://api:3000

  api:
    image: node:18
    # Can reach db at postgres://db:5432

  db:
    image: postgres
```

### Custom Networks
```yaml
services:
  web:
    networks:
      - frontend

  api:
    networks:
      - frontend
      - backend

  db:
    networks:
      - backend

networks:
  frontend:
  backend:
```

### External Network
```yaml
# Connect to existing network
networks:
  existing:
    external: true
    name: my-existing-network
```

### Network Aliases
```yaml
services:
  db:
    networks:
      backend:
        aliases:
          - database
          - postgres
```

---

## Volumes and Data

### Types of Volumes
```yaml
services:
  app:
    volumes:
      # Named volume (managed by Docker)
      - mydata:/app/data

      # Bind mount (host path)
      - ./config:/app/config

      # Read-only bind mount
      - ./config:/app/config:ro

      # Anonymous volume (not persisted)
      - /app/temp

volumes:
  mydata:
```

### Volume Options
```yaml
volumes:
  mydata:
    driver: local
    driver_opts:
      type: none
      device: /path/on/host
      o: bind
```

### Backup Volume Data
```bash
# Backup a volume
docker run --rm -v mydata:/data -v $(pwd):/backup alpine \
  tar cvf /backup/mydata.tar /data

# Restore a volume
docker run --rm -v mydata:/data -v $(pwd):/backup alpine \
  tar xvf /backup/mydata.tar -C /
```

---

## Environment Variables

### Methods
```yaml
services:
  app:
    # Inline
    environment:
      - NODE_ENV=production
      - API_KEY=abc123

    # From file
    env_file:
      - .env
      - .env.local

    # From shell (uses current shell value)
    environment:
      - API_KEY  # Uses $API_KEY from shell
```

### .env File
```bash
# .env
NODE_ENV=development
API_KEY=your-api-key
DB_PASSWORD=secret
```

### Variable Substitution
```yaml
services:
  app:
    image: myapp:${TAG:-latest}
    ports:
      - "${PORT:-3000}:3000"
```

### Secrets (Production)
```yaml
services:
  app:
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

---

## Production Tips

### Override Files
```bash
# docker-compose.yml (base)
# docker-compose.override.yml (dev - auto-loaded)
# docker-compose.prod.yml (production)

# Development (auto-loads override)
docker-compose up -d

# Production
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

### Production Compose File
```yaml
# docker-compose.prod.yml
services:
  app:
    restart: always
    deploy:
      resources:
        limits:
          cpus: "1"
          memory: 1G
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

### Health Checks
```yaml
services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Wait for Dependencies
```yaml
services:
  app:
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5
```

---

## Troubleshooting Scenarios

### Scenario 1: "Container Keeps Restarting"

**Situation:** Service starts and immediately stops in a loop.

```bash
# Step 1: Check logs for error
docker-compose logs web

# Step 2: See exit code
docker-compose ps -a
# Exit code 1 = app error, 137 = OOM killed

# Step 3: Run interactively to debug
docker-compose run --rm web sh
# Now you can manually run the start command and see errors
```

**Common causes:**
- Missing environment variable
- Can't connect to database (not ready yet)
- Permission error on mounted volume
- OOM (out of memory) - increase memory limit

---

### Scenario 2: "Changes Not Reflected After Edit"

**Situation:** You edited code but container shows old version.

```bash
# If using bind mount (volumes: ./src:/app)
# Changes should be instant for interpreted languages

# If it's a built image, rebuild:
docker-compose up -d --build

# Nuclear option - fresh everything:
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

---

### Scenario 3: "App Can't Connect to Database"

**Situation:** `connection refused` or `host not found` errors.

```bash
# Step 1: Is DB actually running?
docker-compose ps

# Step 2: Check DB logs
docker-compose logs db

# Step 3: Can you connect from app container?
docker-compose exec web sh
ping db                    # Should resolve
nc -zv db 5432            # Port open?

# Step 4: Check your connection string
# Use service name, not localhost!
# Wrong: postgres://localhost:5432
# Right: postgres://db:5432
```

**Common fixes:**
- Use service name as hostname (not `localhost`)
- Add `depends_on` to wait for DB
- Add healthcheck for proper wait

---

### Scenario 4: "Port Already in Use"

**Situation:** `bind: address already in use` error.

```bash
# Step 1: Find what's using the port
lsof -i :3000
# or
docker ps | grep 3000

# Step 2: Stop the other container/process
docker stop container-name
# or
kill $(lsof -t -i :3000)

# Step 3: Or change your port mapping
ports:
  - "3001:3000"  # Use different host port
```

---

### Scenario 5: "Volume Data Not Persisting"

**Situation:** Data disappears when you restart containers.

```bash
# Check if using named volume vs bind mount
docker-compose config | grep -A5 volumes

# Named volume (persists):
volumes:
  - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:

# Anonymous volume (doesn't persist):
volumes:
  - /var/lib/postgresql/data

# Check if volume exists
docker volume ls | grep postgres
```

---

### Scenario 6: "Out of Disk Space"

**Situation:** Docker errors about disk space.

```bash
# Step 1: Check Docker disk usage
docker system df

# Step 2: Clean up this project
docker-compose down -v --rmi all

# Step 3: Clean up system-wide
docker system prune -a --volumes -f
```

---

## Quick Reference

| Task | Command |
|------|---------|
| Start services | `docker-compose up -d` |
| Stop services | `docker-compose down` |
| View logs | `docker-compose logs -f` |
| Rebuild and start | `docker-compose up -d --build` |
| Shell into service | `docker-compose exec web bash` |
| Run one-off command | `docker-compose run --rm web npm test` |
| List services | `docker-compose ps` |
| Stop + remove volumes | `docker-compose down -v` |
| Scale service | `docker-compose up -d --scale web=3` |

