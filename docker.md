# Docker Documentation

## Introduction
Docker is a platform for developing, shipping, and running applications inside containers. Containers are lightweight, portable, and ensure consistent execution environments.

---

## Table of Contents
1. [Images](#images)
2. [Containers](#containers)
3. [Dockerfile](#dockerfile)
4. [Common Commands](#common-commands)
5. [Docker Hub: Login and Push](#docker-hub-login-and-push)
6. [Networking](#networking)
7. [Volumes](#volumes)
8. [Ports](#ports)
9. [Multi-Stage Builds](#multi-stage-builds)
10. [Docker Compose](#docker-compose)
11. [Python Project with Debug Configuration](#python-project-with-debug-configuration)
12. [Security Best Practices](#security-best-practices)
13. [Resources](#resources)

---

## Images
Docker images are immutable templates used to create containers.

### Commands for Images
- **Pull an image**:
  ```bash
  docker pull ubuntu:20.04
  ```
- **List all images**:
  ```bash
  docker images
  ```
- **Remove an image**:
  ```bash
  docker rmi <image_id>
  ```

---

## Containers
Containers are instances of images that run applications in isolated environments.

### Commands for Containers
- **Run a container interactively**:
  ```bash
  docker run -it ubuntu:20.04 bash
  ```
- **List all containers (running and stopped)**:
  ```bash
  docker ps -a
  ```
- **Stop a running container**:
  ```bash
  docker stop <container_id>
  ```
- **Remove a container**:
  ```bash
  docker rm <container_id>
  ```

---

## Dockerfile
A `Dockerfile` is a script of instructions to build a Docker image.

### Example Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### Build and Run a Dockerfile
- **Build an image**:
  ```bash
  docker build -t my_app .
  ```
- **Run a container from the built image**:
  ```bash
  docker run -p 3000:3000 my_app
  ```

---

## Common Commands
- **List running containers**:
  ```bash
  docker ps
  ```
- **List all containers (including stopped)**:
  ```bash
  docker ps -a
  ```
- **View container logs**:
  ```bash
  docker logs <container_id>
  ```
- **Inspect a container**:
  ```bash
  docker inspect <container_id>
  ```

---

## Docker Hub: Login and Push
Docker Hub is a registry for hosting and sharing Docker images.

### Commands for Docker Hub
- **Login to Docker Hub**:
  ```bash
  docker login
  ```
  Enter your username and password when prompted.

- **Tag an image**:
  ```bash
  docker tag my_app <your_dockerhub_username>/my_app:latest
  ```

- **Push an image to Docker Hub**:
  ```bash
  docker push <your_dockerhub_username>/my_app:latest
  ```

---

## Networking
Docker networking allows containers to communicate with each other and the outside world.

### Commands for Networking
- **List all networks**:
  ```bash
  docker network ls
  ```
- **Create a custom network**:
  ```bash
  docker network create my_network
  ```
- **Connect a container to a network**:
  ```bash
  docker network connect my_network <container_id>
  ```
- **Inspect a network**:
  ```bash
  docker network inspect my_network
  ```

---

## Volumes
Volumes persist data beyond the lifecycle of a container.

### Commands for Volumes
- **List volumes**:
  ```bash
  docker volume ls
  ```
- **Create a volume**:
  ```bash
  docker volume create my_volume
  ```
- **Mount a volume**:
  ```bash
  docker run -v my_volume:/app/data <image_name>
  ```
- **Bind mount a host directory**:
  ```bash
  docker run -v /host/path:/container/path <image_name>
  ```

---

## Ports
Expose container ports to the host for external access.

### Commands for Ports
- **Expose a container port to the host**:
  ```bash
  docker run -p 8080:80 nginx
  ```
- **Inspect port mappings**:
  ```bash
  docker port <container_id>
  ```

---

## Multi-Stage Builds
Optimize Docker images by using multiple stages in a Dockerfile.

### Example Multi-Stage Dockerfile
```dockerfile
# Stage 1: Build
FROM python:3.9 as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
RUN python -m compileall .

# Stage 2: Production
FROM python:3.9-slim
WORKDIR /app
COPY --from=builder /app /app
CMD ["python", "app.py"]
```

---

## Docker Compose
Docker Compose simplifies multi-container applications.

### Example `docker-compose.yml`
```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app
  db:
    image: postgres
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
```

### Commands for Docker Compose
- **Start services**:
  ```bash
  docker-compose up
  ```
- **Stop services**:
  ```bash
  docker-compose down
  ```

---

## Python Project with Debug Configuration
Here’s an example Python project with a debug configuration using Docker.

### Project Structure
```
my_project/
├── app.py
├── Dockerfile
├── requirements.txt
└── docker-compose.yml
```

### Example `app.py`
```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello, Docker!"

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=5000)
```

### Example `requirements.txt`
```
flask
```

### Example Dockerfile
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### Example `docker-compose.yml`
```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app
    environment:
      FLASK_ENV: development
```

---

## Security Best Practices
- Use minimal base images like `alpine`.
- Avoid running containers as root:
  ```dockerfile
  USER nonroot
  ```
- Scan images for vulnerabilities:
  ```bash
  docker scan <image_name>
  ```
- Keep secrets out of images (use environment variables or external secret managers).

---

## Resources
- [Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Best Practices Guide](https://docs.docker.com/develop/dev-best-practices/)

---

Happy Containerizing!
