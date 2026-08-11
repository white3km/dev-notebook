# Docker CLI - Cheatsheet

**Last Updated: 2026-08

## Images

| Command | Description |
|---|---|
| `docker images` | List local images |
| `docker pull <image>` | Download an image |
| `docker build -t <name>:<tag> .` | Build an image |
| `docker rmi <image>` | Remove an image |
| `docker image inspect <image>` | Show image details |
| `docker image history <image>` | Show image layers |
| `docker tag <image> <repo>:<tag>` | Tag an image |
| `docker push <repo>:<tag>` | Push image to a registry |

```bash
# Build an image
docker build -t myapp:latest .

# Build without using the cache
docker build --no-cache -t myapp:latest .

# Remove unused images
docker image prune

# Remove all unused images
docker image prune -a
```

---

## Containers

| Command | Description |
|---|---|
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker run <image>` | Create and start a container |
| `docker start <container>` | Start an existing container |
| `docker stop <container>` | Stop a container |
| `docker restart <container>` | Restart a container |
| `docker rm <container>` | Remove a container |
| `docker logs <container>` | View container logs |
| `docker inspect <container>` | Show container details |
| `docker stats` | Show resource usage |
| `docker top <container>` | Show processes running in container |

### Common `docker run` Options

```bash
docker run -d --name myapp myimage
```

| Option | Description |
|---|---|
| `-d` | Run in background (detached) |
| `-it` | Interactive terminal |
| `--name <name>` | Assign container name |
| `-p <host>:<container>` | Map a port |
| `-v <host>:<container>` | Mount a volume |
| `-e KEY=value` | Set environment variable |
| `--env-file <file>` | Load environment variables |
| `--rm` | Automatically remove container when it exits |
| `--network <network>` | Connect to a network |
| `--restart <policy>` | Configure restart behavior |

```bash
# Run interactively
docker run -it ubuntu bash

# Run in background with port mapping
docker run -d --name web -p 8080:80 nginx

# Pass environment variables
docker run -d -e APP_ENV=production myapp

# Mount a directory
docker run -d -v ./data:/app/data myapp
```

---

## Executing Commands

```bash
# Open a shell in a running container
docker exec -it <container> bash

# If bash isn't available
docker exec -it <container> sh

# Run a specific command
docker exec <container> ls -la

# Run as root
docker exec -u root -it <container> bash
```

---

## Logs

```bash
# View logs
docker logs <container>

# Follow logs
docker logs -f <container>

# Show timestamps
docker logs -t <container>

# Show last 100 lines
docker logs --tail 100 <container>

# Logs since a specific time
docker logs --since 1h <container>
```

---

## Networks

| Command | Description |
|---|---|
| `docker network ls` | List networks |
| `docker network create <name>` | Create a network |
| `docker network inspect <network>` | Show network details |
| `docker network connect <network> <container>` | Connect container |
| `docker network disconnect <network> <container>` | Disconnect container |
| `docker network rm <network>` | Remove network |
| `docker network prune` | Remove unused networks |

```bash
# Create a network
docker network create mynetwork

# Run containers on the same network
docker run -d --name db --network mynetwork postgres
docker run -d --name app --network mynetwork myapp

# Containers can communicate using container names
# Example: postgres://db:5432/mydb
```

---

## Volumes

| Command | Description |
|---|---|
| `docker volume ls` | List volumes |
| `docker volume create <name>` | Create volume |
| `docker volume inspect <name>` | Show volume details |
| `docker volume rm <name>` | Remove volume |
| `docker volume prune` | Remove unused volumes |

```bash
# Create and mount a volume
docker volume create app-data
docker run -v app-data:/app/data myapp
```

---

## Docker Compose

Modern Docker uses `docker compose` rather than the older `docker-compose` command.

| Command | Description |
|---|---|
| `docker compose up` | Create and start services |
| `docker compose up -d` | Start in background |
| `docker compose down` | Stop and remove services |
| `docker compose ps` | List services |
| `docker compose logs` | View service logs |
| `docker compose logs -f` | Follow logs |
| `docker compose build` | Build images |
| `docker compose pull` | Pull images |
| `docker compose restart` | Restart services |
| `docker compose exec <service> <cmd>` | Execute command |
| `docker compose config` | Validate/render configuration |

```bash
# Start application
docker compose up -d

# Rebuild and start
docker compose up -d --build

# View logs
docker compose logs -f

# Open shell in a service
docker compose exec app bash

# Stop and remove containers/networks
docker compose down

# Stop and remove containers, networks, and volumes
docker compose down -v
```

---

## Registry / Authentication

```bash
# Log in to a registry
docker login

# Pull an image
docker pull nginx:latest

# Tag an image for a registry
docker tag myapp:latest username/myapp:latest

# Push an image
docker push username/myapp:latest

# Log out
docker logout
```

---

## Cleanup

```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune

# Remove most unused Docker resources
docker system prune

# Remove unused resources including unused images
docker system prune -a

# See Docker disk usage
docker system df
```

> **Warning:** `docker system prune -a` can remove images you may want later. Use it carefully.

---

## Useful Inspection Commands

```bash
# Docker version
docker version

# Docker system information
docker info

# Inspect an image
docker image inspect <image>

# Inspect a container
docker inspect <container>

# Inspect a network
docker network inspect <network>

# Inspect a volume
docker volume inspect <volume>

# View Docker disk usage
docker system df
```

---

## Common Workflows

### Build -> Run -> Debug

```bash
docker build -t myapp:latest .

docker run -d \
  --name myapp \
  -p 8080:8080 \
  myapp:latest

docker ps

docker logs -f myapp

docker exec -it myapp bash

docker stop myapp
docker rm myapp
```

### Rebuild a Compose Application

```bash
docker compose down
docker compose build
docker compose up -d
docker compose logs -f
```

### Find a Container's IP

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>
```

### Copy Files

```bash
# Container -> host
docker cp <container>:/app/file.txt ./file.txt

# Host -> container
docker cp ./file.txt <container>:/app/file.txt
```

### Rename a Container

```bash
docker rename <old-name> <new-name>
```

---

## Command Structure

Most Docker commands follow this pattern:

```text
docker <object> <command> [options] [arguments]
```

Examples:

```bash
docker container ls
docker image ls
docker network ls
docker volume ls
```

Shorter commonly used commands include:

```bash
docker ps
docker images
docker run
docker exec
docker logs
```

---

## Quick Troubleshooting

```bash
docker ps -a                 # Is the container running/exited?
docker logs <container>      # Why did it fail?
docker inspect <container>   # Configuration/details
docker images                # Is the image present?
docker network ls            # What networks exist?
docker system df             # Where is disk space going?
docker stats                 # Is it using excessive resources?
```
