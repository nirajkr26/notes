# Docker — Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Docker** is an open-source platform that automates the deployment, scaling, and management of applications using **containerization**. It packages an application and all its dependencies into a single portable unit called a **container**.

**Why Docker?**
- Eliminates "works on my machine" issues
- Lightweight compared to virtual machines (VMs)
- Fast startup times (seconds vs minutes for VMs)
- Consistent environments across development, staging, and production
- Easy horizontal scaling and microservices architecture

**Docker vs Virtual Machines:**

| Feature              | Docker (Containers)              | Virtual Machines                |
|----------------------|----------------------------------|---------------------------------|
| Isolation            | Process-level (namespaces/cgroups) | Hardware-level (hypervisor)    |
| OS                   | Shares host OS kernel            | Full guest OS per VM            |
| Size                 | MBs (lightweight)                | GBs (heavy)                     |
| Startup time         | Seconds                          | Minutes                         |
| Performance overhead | Minimal                          | Higher (hardware emulation)     |
| Portability          | Highly portable                  | Less portable (OS-dependent)    |
| Use case             | Microservices, CI/CD pipelines   | Legacy apps, full OS isolation  |

---

## 2. Core Concepts & Architecture

### 2.1 Key Terminology

| Term                | Definition                                                                 |
|---------------------|----------------------------------------------------------------------------|
| **Image**           | Read-only template with instructions to create a container (layered FS)    |
| **Container**       | Runnable instance of an image; isolated process with its own filesystem    |
| **Dockerfile**      | Text file with build instructions to create a Docker image                 |
| **Registry**        | Repository to store and distribute images (Docker Hub, ECR, GCR)          |
| **Layer**           | Each instruction in a Dockerfile creates a new read-only layer             |
| **Union File System**| Overlays multiple layers to form a single unified filesystem view          |
| **Volume**          | Persistent data storage outside the container's lifecycle                  |
| **Network**         | Virtual networks that allow containers to communicate                      |
| **Docker Daemon**   | Background service (`dockerd`) managing images, containers, networks, volumes |
| **Docker Client**   | CLI tool (`docker`) that sends commands to the daemon via REST API         |
| **Docker Engine**   | Core runtime consisting of daemon, REST API, and CLI client                |

### 2.2 Docker Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     Docker Host                          │
│                                                         │
│  ┌──────────────┐    REST API    ┌──────────────────┐  │
│  │ Docker Client│◄──────────────►│  Docker Daemon   │  │
│  │  (docker CLI)│                │   (dockerd)      │  │
│  └──────────────┘                └────────┬─────────┘  │
│                                           │             │
│                          ┌────────────────┼──────────┐  │
│                          │                │           │  │
│                   ┌──────▼──┐  ┌──────────▼──┐       │  │
│                   │ Images  │  │ Containers  │       │  │
│                   └─────────┘  └─────────────┘       │  │
│                          │                            │  │
│                   ┌──────▼─────────────┐              │  │
│                   │ Volumes / Networks │              │  │
│                   └────────────────────┘              │  │
└─────────────────────────────────────────────────────────┘
                           │
              ┌────────────▼────────────┐
              │  Docker Registry        │
              │  (Docker Hub / ECR/GCR) │
              └─────────────────────────┘
```

### 2.3 Container Internals

Docker containers rely on Linux kernel features:

| Feature            | Purpose                                                               |
|--------------------|-----------------------------------------------------------------------|
| **Namespaces**     | Isolate PID, network, mount, UTS (hostname), IPC, user namespaces    |
| **cgroups**        | Limit and monitor resource usage (CPU, memory, I/O, network)          |
| **Union FS (OverlayFS)** | Layer-based filesystem allowing copy-on-write                  |
| **seccomp**        | Restrict system calls available to container processes               |
| **AppArmor/SELinux** | Mandatory access control for additional security                   |

---

## 3. Dockerfile

A **Dockerfile** is a script of instructions used to build a Docker image.

### 3.1 Common Dockerfile Instructions

| Instruction    | Purpose                                                             | Example                                  |
|----------------|---------------------------------------------------------------------|------------------------------------------|
| `FROM`         | Sets the base image (must be first instruction)                     | `FROM node:18-alpine`                    |
| `RUN`          | Executes commands in a new layer during build                       | `RUN apt-get update && apt-get install -y curl` |
| `CMD`          | Default command when container starts (can be overridden)           | `CMD ["node", "app.js"]`                 |
| `ENTRYPOINT`   | Fixed executable for the container (CMD provides args)              | `ENTRYPOINT ["python", "server.py"]`     |
| `COPY`         | Copies files from host to image                                     | `COPY . /app`                            |
| `ADD`          | Like COPY but also handles URLs and tar extraction                  | `ADD app.tar.gz /app`                    |
| `ENV`          | Sets environment variables                                          | `ENV PORT=3000`                          |
| `ARG`          | Build-time variable (not available at runtime)                      | `ARG VERSION=1.0`                        |
| `EXPOSE`       | Documents which port the container listens on                       | `EXPOSE 8080`                            |
| `WORKDIR`      | Sets working directory for subsequent instructions                  | `WORKDIR /app`                           |
| `VOLUME`       | Creates a mount point for external volumes                          | `VOLUME ["/data"]`                       |
| `USER`         | Sets the user for subsequent instructions                           | `USER node`                              |
| `LABEL`        | Adds metadata (key-value pairs) to the image                        | `LABEL version="1.0"`                    |
| `HEALTHCHECK`  | Defines how to test if container is healthy                         | `HEALTHCHECK CMD curl -f http://localhost/` |
| `ONBUILD`      | Trigger for when image is used as a base                            | `ONBUILD RUN npm install`                |

### 3.2 CMD vs ENTRYPOINT

| Aspect           | `CMD`                                   | `ENTRYPOINT`                            |
|------------------|-----------------------------------------|-----------------------------------------|
| Overridable?     | Yes — easily overridden at runtime      | No — only arguments can be appended     |
| Purpose          | Default command / arguments             | Fixed executable that always runs       |
| Combined usage   | CMD provides default args to ENTRYPOINT | ENTRYPOINT sets executable              |
| Shell form       | `CMD echo hello`                        | `ENTRYPOINT echo hello`                 |
| Exec form        | `CMD ["echo", "hello"]`                 | `ENTRYPOINT ["echo", "hello"]`          |

```dockerfile
# ENTRYPOINT + CMD combo (best practice)
ENTRYPOINT ["python", "app.py"]
CMD ["--port", "8080"]
# Running: docker run myimage --port 9090
# Executes: python app.py --port 9090
```

### 3.3 Multi-Stage Build (Best Practice)

Multi-stage builds reduce final image size by separating build and runtime environments:

```dockerfile
# Stage 1: Build
FROM maven:3.9-openjdk-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

# Stage 2: Runtime (much smaller image)
FROM openjdk:17-jre-slim
WORKDIR /app
COPY --from=builder /app/target/app.jar .
EXPOSE 8080
USER nobody
CMD ["java", "-jar", "app.jar"]
```

### 3.4 Node.js Dockerfile Best Practice

```dockerfile
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy dependency files first (for layer caching)
COPY package*.json ./
RUN npm ci --only=production

# Copy source code
COPY . .

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

EXPOSE 3000
CMD ["node", "server.js"]
```

### 3.5 .dockerignore

Similar to `.gitignore`; prevents unnecessary files from being added to the build context:

```
node_modules
.git
.env
*.log
Dockerfile
docker-compose.yml
README.md
```

---

## 4. Docker CLI — Essential Commands

### 4.1 Image Commands

```bash
# Build an image from Dockerfile
docker build -t myapp:1.0 .
docker build -t myapp:1.0 -f Dockerfile.prod .   # specify Dockerfile

# List images
docker images
docker image ls

# Pull image from registry
docker pull nginx:latest

# Push image to registry
docker push username/myapp:1.0

# Tag image
docker tag myapp:1.0 username/myapp:1.0

# Remove image
docker rmi myapp:1.0
docker image prune          # remove dangling images
docker image prune -a       # remove all unused images

# Inspect image
docker inspect myapp:1.0
docker image history myapp:1.0   # show layers
```

### 4.2 Container Commands

```bash
# Run a container
docker run nginx                              # foreground
docker run -d nginx                           # detached (background)
docker run -d -p 8080:80 nginx                # port mapping host:container
docker run -d --name webserver nginx          # named container
docker run -it ubuntu bash                    # interactive terminal
docker run --rm ubuntu echo "hello"           # auto-remove after exit
docker run -e DB_HOST=localhost myapp         # environment variable
docker run -v /host/path:/container/path myapp  # bind mount
docker run --memory="512m" --cpus="1.5" myapp   # resource limits

# List containers
docker ps           # running
docker ps -a        # all (including stopped)

# Stop / Start / Restart
docker stop <id|name>
docker start <id|name>
docker restart <id|name>
docker kill <id|name>    # sends SIGKILL immediately

# Remove containers
docker rm <id|name>
docker rm -f <id|name>   # force remove running container
docker container prune   # remove all stopped containers

# Execute command in running container
docker exec -it <id|name> bash
docker exec <id|name> env

# View logs
docker logs <id|name>
docker logs -f <id|name>        # follow (tail -f)
docker logs --tail 100 <id|name>

# Copy files between host and container
docker cp localfile.txt <id>:/app/file.txt
docker cp <id>:/app/file.txt ./localfile.txt

# Inspect container
docker inspect <id|name>
docker stats <id|name>           # live resource usage
docker top <id|name>             # running processes

# Pause / Unpause
docker pause <id|name>
docker unpause <id|name>
```

### 4.3 System Commands

```bash
docker info                 # system-wide information
docker version              # client and daemon versions
docker system df            # disk usage
docker system prune         # remove unused data
docker system prune -a      # remove all unused data (images, containers, volumes, networks)
docker events               # real-time events from daemon
```

---

## 5. Docker Networking

### 5.1 Network Types

| Driver     | Description                                                          | Use Case                          |
|------------|----------------------------------------------------------------------|-----------------------------------|
| **bridge** | Default; containers on same bridge can communicate by IP             | Single-host container communication |
| **host**   | Container shares host network stack; no network isolation            | Performance-critical, no port mapping needed |
| **none**   | Disables networking entirely                                         | Isolated tasks, batch jobs        |
| **overlay**| Multi-host networking; used by Docker Swarm                          | Distributed services across hosts |
| **macvlan**| Assigns container a MAC address; appears as physical device on network | Legacy apps needing direct network access |

### 5.2 Network Commands

```bash
# List networks
docker network ls

# Create a custom bridge network
docker network create mynetwork
docker network create --driver bridge --subnet 172.20.0.0/16 mynetwork

# Run container on specific network
docker run -d --network mynetwork --name app1 myapp

# Connect running container to network
docker network connect mynetwork app1

# Disconnect from network
docker network disconnect mynetwork app1

# Inspect network
docker network inspect mynetwork

# Remove network
docker network rm mynetwork
docker network prune    # remove unused networks
```

### 5.3 Container Communication

```bash
# Containers on the same user-defined bridge can reach each other by name
docker run -d --network mynetwork --name db postgres
docker run -d --network mynetwork --name app myapp
# 'app' container can reach 'db' at hostname 'db'
```

### 5.4 Port Mapping

```
-p <host_port>:<container_port>
-p 8080:80          → host:8080 → container:80
-p 127.0.0.1:8080:80 → bind to specific host IP
-P                   → auto-map all EXPOSE'd ports to random host ports
```

---

## 6. Docker Volumes & Storage

### 6.1 Storage Types

| Type          | Description                                               | Managed by  | Use Case                         |
|---------------|-----------------------------------------------------------|-------------|----------------------------------|
| **Volume**    | Docker-managed directory in `/var/lib/docker/volumes/`    | Docker      | Persistent data, database storage |
| **Bind Mount**| Maps host directory/file directly into container          | User/OS     | Development, config injection    |
| **tmpfs**     | Stored in host memory only; lost on container stop        | Docker/OS   | Sensitive data, temp files        |

### 6.2 Volume Commands

```bash
# Create volume
docker volume create mydata

# List volumes
docker volume ls

# Inspect volume
docker volume inspect mydata

# Remove volume
docker volume rm mydata
docker volume prune    # remove unused volumes

# Run with volume
docker run -d -v mydata:/app/data postgres         # named volume
docker run -d -v /host/path:/app/data postgres     # bind mount
docker run -d --mount type=volume,src=mydata,dst=/app/data postgres  # --mount syntax (explicit)
docker run -d --mount type=tmpfs,dst=/tmp postgres  # tmpfs mount
```

### 6.3 Volume vs Bind Mount

| Feature           | Volume                          | Bind Mount                    |
|-------------------|---------------------------------|-------------------------------|
| Managed by        | Docker                          | Host OS                       |
| Portability       | High (no host path dependency)  | Low (host-specific path)      |
| Performance       | Optimal (Docker manages I/O)    | Good                          |
| Backup            | `docker volume` commands        | Standard OS file operations   |
| Sharing across hosts | Via plugins (NFS, EFS, etc.)| Manual                        |
| Dev use case      | Production data                 | Source code hot-reload        |

---

## 7. Docker Compose

**Docker Compose** is a tool for defining and running multi-container applications using a YAML file (`docker-compose.yml`).

### 7.1 docker-compose.yml Structure

```yaml
version: '3.9'

services:
  # Web application service
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: myapp:latest
    container_name: my-app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=db
      - DB_PORT=5432
    env_file:
      - .env
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app-network
    volumes:
      - ./logs:/app/logs
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  # Database service
  db:
    image: postgres:15-alpine
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Cache service
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    ports:
      - "6379:6379"
    networks:
      - app-network
    command: redis-server --appendonly yes
    volumes:
      - redisdata:/data

networks:
  app-network:
    driver: bridge

volumes:
  pgdata:
  redisdata:
```

### 7.2 Docker Compose Commands

```bash
# Start services (build if needed)
docker compose up
docker compose up -d               # detached
docker compose up --build          # rebuild images
docker compose up --scale app=3   # scale a service

# Stop services
docker compose down                 # stop and remove containers, networks
docker compose down -v              # also remove volumes
docker compose stop                 # stop without removing

# View status and logs
docker compose ps
docker compose logs
docker compose logs -f app          # follow logs of 'app' service

# Execute command in service
docker compose exec app bash

# Build images
docker compose build
docker compose build --no-cache

# Pull latest images
docker compose pull
```

### 7.3 Compose File Keywords

| Keyword        | Purpose                                                   |
|----------------|-----------------------------------------------------------|
| `services`     | Defines all containers                                    |
| `networks`     | Custom networks for inter-service communication           |
| `volumes`      | Named volumes for persistent storage                      |
| `build`        | Build context / Dockerfile path                           |
| `image`        | Pre-built image name                                      |
| `ports`        | Host:container port mapping                               |
| `environment`  | Environment variables                                     |
| `depends_on`   | Service startup order (with optional conditions)          |
| `restart`      | Restart policy (no, always, unless-stopped, on-failure)   |
| `healthcheck`  | Container health check configuration                      |
| `deploy`       | Deployment config (replicas, resources) for Swarm         |
| `profiles`     | Conditionally include services                            |
| `secrets`      | Mount Docker secrets into services                        |
| `configs`      | Mount configs (non-sensitive data)                        |

---

## 8. Docker Registry

### 8.1 Docker Hub

- Default public registry (`hub.docker.com`)
- Image naming: `[username/]<repository>:<tag>`
- Official images (e.g., `nginx`, `postgres`) have no username prefix

```bash
# Login/logout
docker login
docker logout
docker login registry.example.com   # private registry

# Push / Pull
docker push username/myapp:1.0
docker pull ubuntu:22.04
```

### 8.2 Private Registry

```bash
# Run local registry
docker run -d -p 5000:5000 --name registry registry:2

# Tag and push to local registry
docker tag myapp:1.0 localhost:5000/myapp:1.0
docker push localhost:5000/myapp:1.0

# Pull from local registry
docker pull localhost:5000/myapp:1.0
```

### 8.3 Common Cloud Registries

| Registry | Provider | URL Pattern                      |
|----------|----------|----------------------------------|
| Docker Hub | Docker | `docker.io/username/repo`       |
| Amazon ECR | AWS   | `<account>.dkr.ecr.<region>.amazonaws.com` |
| Google GCR | GCP   | `gcr.io/<project>/repo`         |
| Azure ACR | Azure  | `<registry>.azurecr.io/repo`    |
| GitHub GHCR | GitHub | `ghcr.io/<username>/repo`     |

---

## 9. Docker Security

### 9.1 Best Practices

| Practice                     | Description                                                   |
|------------------------------|---------------------------------------------------------------|
| **Use minimal base images**  | Alpine, distroless to reduce attack surface                   |
| **Run as non-root user**     | `USER` instruction or `--user` flag; never run as root        |
| **Read-only filesystem**     | `--read-only` flag; reduces container mutability              |
| **No privileged mode**       | Avoid `--privileged`; grants full host capabilities           |
| **Limit capabilities**       | `--cap-drop ALL --cap-add <specific>` (principle of least privilege) |
| **Scan images for CVEs**     | Trivy, Snyk, Docker Scout, Clair                              |
| **Secrets management**       | Never bake secrets into images; use Docker secrets or vaults  |
| **Pin image versions**       | Use `nginx:1.25.3` not `nginx:latest`                        |
| **Multi-stage builds**       | Keep build tools out of production images                     |
| **Network policies**         | Use user-defined networks; don't use `--network host`         |
| **Resource limits**          | Always set `--memory` and `--cpus` to prevent resource exhaustion |

### 9.2 Docker Security Flags

```bash
# Run as specific user
docker run --user 1000:1000 myapp

# Read-only root filesystem
docker run --read-only myapp

# Drop all capabilities, add only what's needed
docker run --cap-drop ALL --cap-add NET_BIND_SERVICE nginx

# Disable privilege escalation
docker run --security-opt no-new-privileges myapp

# Use seccomp profile
docker run --security-opt seccomp=/path/to/profile.json myapp

# Set resource limits
docker run --memory="256m" --memory-swap="512m" --cpus="0.5" myapp
```

### 9.3 Scanning Images

```bash
# Docker Scout (built-in)
docker scout cves myapp:1.0

# Trivy
trivy image myapp:1.0
```

---

## 10. Docker Swarm

**Docker Swarm** is Docker's built-in container orchestration tool for managing a cluster of Docker nodes.

### 10.1 Key Concepts

| Term          | Description                                                    |
|---------------|----------------------------------------------------------------|
| **Swarm**     | Cluster of Docker nodes managed together                        |
| **Node**      | Individual Docker host in the swarm (manager or worker)        |
| **Manager**   | Controls cluster state, schedules services, maintains Raft     |
| **Worker**    | Executes tasks assigned by manager                             |
| **Service**   | Desired state definition (image, replicas, ports)              |
| **Task**      | Atomic unit of work (one container running a service replica)  |
| **Stack**     | Group of related services defined in a Compose file            |

### 10.2 Swarm Commands

```bash
# Initialize swarm
docker swarm init --advertise-addr <MANAGER-IP>

# Join as worker
docker swarm join --token <WORKER-TOKEN> <MANAGER-IP>:2377

# Create service
docker service create --name web --replicas 3 -p 80:80 nginx

# Scale service
docker service scale web=5

# List services
docker service ls
docker service ps web    # show tasks

# Update service (rolling update)
docker service update --image nginx:1.25 web

# Remove service
docker service rm web

# Deploy stack from Compose file
docker stack deploy -c docker-compose.yml mystack
docker stack ls
docker stack services mystack
docker stack rm mystack
```

---

## 11. Docker vs Kubernetes (Comparison)

| Feature              | Docker Swarm                    | Kubernetes (K8s)                     |
|----------------------|---------------------------------|--------------------------------------|
| Setup complexity     | Easy                            | Complex                              |
| Auto-scaling         | Manual scaling                  | HPA / VPA / KEDA                     |
| Self-healing         | Basic                           | Advanced (liveness/readiness probes) |
| Load balancing       | Built-in                        | Ingress + Services                   |
| Rolling updates      | Supported                       | Sophisticated (canary, blue-green)   |
| Ecosystem            | Smaller                         | Huge (CNCF ecosystem)                |
| Production readiness | Small-medium workloads          | Enterprise / large-scale workloads   |
| Storage              | Volume plugins                  | PV, PVC, StorageClass                |
| Secrets & Config     | Docker Secrets                  | K8s Secrets, ConfigMaps              |

---

## 12. Common Interview Questions

### Q1. What is the difference between an image and a container?
**A:** An **image** is a read-only blueprint (like a class in OOP) built from a Dockerfile. A **container** is a running instance of that image (like an object). Multiple containers can be created from the same image.

### Q2. What happens when you run `docker run`?
**A:**
1. Docker CLI sends request to Docker daemon
2. Daemon checks if image exists locally
3. If not, pulls image from registry
4. Creates a writable container layer on top of the image layers
5. Sets up networking, assigns IP address
6. Starts the process defined by CMD/ENTRYPOINT

### Q3. What is a Docker layer and how does caching work?
**A:** Each instruction in a Dockerfile creates a new layer. Layers are cached; if an instruction and all preceding instructions are unchanged, Docker reuses the cached layer. This speeds up builds significantly. Best practice: put frequently-changing instructions (like `COPY . .`) at the bottom, and stable instructions (like `RUN npm install`) near the top.

### Q4. Difference between COPY and ADD?
**A:** `COPY` simply copies files/directories from host to image. `ADD` does the same but additionally can fetch from remote URLs and automatically extract tar archives. Best practice: prefer `COPY` for clarity; use `ADD` only when you need its extra features.

### Q5. How do containers achieve isolation?
**A:** Via Linux kernel features:
- **Namespaces** — isolate process tree (PID), network, mount points, hostname (UTS), IPC, and user IDs
- **cgroups** — limit and account for resource usage (CPU, memory, I/O)

### Q6. What is the difference between `docker stop` and `docker kill`?
**A:** `docker stop` sends **SIGTERM** first (graceful shutdown), waits 10 seconds, then sends SIGKILL. `docker kill` sends SIGKILL immediately without waiting for graceful shutdown.

### Q7. How do you persist data in Docker?
**A:** Using **volumes** (Docker-managed, best for production) or **bind mounts** (host path mapped into container, good for development). `tmpfs` mounts store data in memory only and are lost when container stops.

### Q8. What is Docker Compose used for?
**A:** Compose defines and manages multi-container applications. It uses a YAML file to configure services, networks, and volumes, allowing you to start the entire application stack with a single command (`docker compose up`).

### Q9. What is a multi-stage build?
**A:** A Dockerfile technique using multiple `FROM` statements. Earlier stages act as build environments; only selected artifacts are copied into the final (smaller) image. This keeps build tools and intermediate files out of the production image.

### Q10. How does Docker networking work?
**A:** Docker creates virtual networks using Linux bridge networking. Containers on the same user-defined bridge network can communicate using container names as hostnames. Docker also supports host, none, overlay (multi-host), and macvlan network drivers.

### Q11. What is the difference between `CMD` and `ENTRYPOINT`?
**A:** `ENTRYPOINT` defines the fixed executable that always runs; `CMD` provides default arguments to it (or the default command if no ENTRYPOINT). `CMD` is easily overridden at runtime, while ENTRYPOINT is not (only its arguments can be overridden).

### Q12. How do you reduce Docker image size?
**A:**
- Use minimal base images (Alpine, distroless)
- Multi-stage builds
- Combine RUN commands to reduce layers
- Use `.dockerignore`
- Remove build dependencies in the same layer
- Avoid installing unnecessary packages

---

## 13. Quick Revision Checklist

- [ ] Image vs Container distinction
- [ ] Dockerfile instructions (FROM, RUN, CMD, ENTRYPOINT, COPY, ADD, ENV, ARG, EXPOSE, WORKDIR, USER, VOLUME)
- [ ] CMD vs ENTRYPOINT behavior
- [ ] Docker build, run, exec, stop, rm, ps commands
- [ ] Port mapping (`-p host:container`)
- [ ] Volumes: named, bind mount, tmpfs
- [ ] Network drivers: bridge, host, none, overlay
- [ ] Docker Compose YAML structure and commands
- [ ] Multi-stage builds
- [ ] Security: non-root user, read-only FS, capabilities, image scanning
- [ ] Layer caching strategy
- [ ] Docker Swarm concepts: service, task, node, manager/worker
- [ ] Namespace and cgroups (container isolation internals)
- [ ] Docker vs Kubernetes trade-offs
