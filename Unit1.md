# Introduction to Docker - Unit 1

## Table of Contents
1. [Docker Hub and Architecture](#docker-hub-and-architecture)
2. [Docker Lifecycle](#docker-lifecycle)
3. [Hypervisor Concepts](#hypervisor-concepts)
4. [Basic Docker Commands](#basic-docker-commands)
5. [Practical Exercises](#practical-exercises)
6. [Advanced Topics](#advanced-topics)

## Docker Hub and Architecture

### Docker Hub Overview
- Cloud-based repository for sharing and managing Docker containers
- Allows users to:
  - Store and download container images
  - Collaborate with development teams
  - Automate builds

### Docker Architecture
```
+-------------------+
|    Docker Client  |
+-------------------+
          |
          v
+-------------------+
|   Docker Daemon   |
+-------------------+
          |
          v
+-------------------+
|    Docker Images  |
+-------------------+
          |
          v
+-------------------+
|   Docker Containers|
+-------------------+
```

## Docker Lifecycle

### Container Lifecycle Stages
1. **Image Pull**: Downloading an image from Docker Hub
2. **Container Creation**: Creating a container from an image
3. **Container Running**: Executing the container
4. **Container Stopping**: Pausing or stopping the container
5. **Container Removal**: Deleting the container

## Hypervisor Concepts

### Types of Hypervisors
- **Type 1 (Bare Metal)**: 
  - Runs directly on host's hardware
  - Examples: VMware ESXi, Microsoft Hyper-V
- **Type 2 (Hosted)**: 
  - Runs on top of host operating system
  - Examples: VirtualBox, VMware Workstation

### Docker vs. Traditional Virtualization
| Feature | Docker | Traditional VM |
|---------|--------|----------------|
| Resource Usage | Lightweight | Heavy |
| Startup Time | Seconds | Minutes |
| Isolation Level | Process-level | Full OS |

## Basic Docker Commands

### Image Management
```bash
# Pull an image
docker pull ubuntu:latest

# List local images
docker images

# Remove an image
docker rmi ubuntu:latest
```

### Container Management
```bash
# Run a container
docker run -d --name my-container ubuntu

# List running containers
docker ps

# Stop a container
docker stop my-container

# Remove a container
docker rm my-container
```

## Practical Exercises

### Exercise 1: Docker Hub Account Setup
1. Visit [Docker Hub](https://hub.docker.com/)
2. Create a new account
3. Verify email
4. Create a repository

### Exercise 2: Pushing Container to Docker Hub
```bash
# Login to Docker Hub
docker login

# Tag your image
docker tag my-image:latest username/repository:tag

# Push image
docker push username/repository:tag
```

### Exercise 3: Creating a Dockerfile
```dockerfile
# Sample Dockerfile
FROM ubuntu:latest
LABEL maintainer="your-email@example.com"

# Update packages
RUN apt-get update && apt-get upgrade -y

# Install necessary tools
RUN apt-get install -y python3 pip

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose port
EXPOSE 8000

# Run application
CMD ["python3", "app.py"]
```
## Advanced Practical Exercises

### Exercise 4: Docker Image Layering and Optimization
**Objective**: Understand Docker image layers and optimize Dockerfile

```dockerfile
# Create a multi-stage Dockerfile
FROM python:3.9 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.9-slim
WORKDIR /app
COPY --from=builder /usr/local/lib/python3.9/site-packages /usr/local/lib/python3.9/site-packages
COPY . .
CMD ["python", "app.py"]
```

**Tasks**:
1. Create a sample Python application
2. Write a multi-stage Dockerfile
3. Build the image and compare size with single-stage Dockerfile
4. Analyze image layers using `docker history`

### Exercise 5: Docker Volume Management
**Objective**: Practice persistent data storage

```bash
# Create a named volume
docker volume create mydata

# Run a container with the volume
docker run -v mydata:/app/data nginx

# Inspect volume details
docker volume inspect mydata

# List all volumes
docker volume ls

# Remove a volume
docker volume rm mydata
```

**Tasks**:
1. Create a volume for a database container
2. Persist data across container restarts
3. Backup and restore volume data

### Exercise 6: Docker Network Configuration
**Objective**: Explore Docker networking modes

```bash
# Create custom bridge network
docker network create myapp-network

# Run containers on custom network
docker run --name webapp --network myapp-network nginx
docker run --name database --network myapp-network postgres

# Inspect network
docker network inspect myapp-network
```

**Tasks**:
1. Create multiple network configurations
2. Test container communication
3. Implement network isolation

### Exercise 7: Docker Compose Complex Application
**Objective**: Build a multi-service application

```yaml
version: '3.8'
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
  backend:
    build: ./backend
    environment:
      - DATABASE_URL=postgres://user:pass@db/mydb
  db:
    image: postgres:13
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=secretpassword

volumes:
  postgres-data:
```

**Tasks**:
1. Create a full-stack application
2. Configure service dependencies
3. Manage environment variables
4. Implement volume persistence

### Exercise 8: Containerized Development Environment
**Objective**: Use Docker for consistent development setup

```dockerfile
FROM python:3.9-dev
WORKDIR /workspace
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

**Tasks**:
1. Dockerize a development environment
2. Create a reproducible setup
3. Use Docker for local development

### Exercise 9: Docker Security Scanning
**Objective**: Implement container security practices

```bash
# Install Docker Scan CLI
docker scan --help

# Scan an image for vulnerabilities
docker scan nginx:latest

# Generate a detailed security report
docker scan --severity high nginx:latest
```

**Tasks**:
1. Scan official and custom images
2. Identify and fix security vulnerabilities
3. Implement best practices for secure containers

### Exercise 10: Continuous Integration with Docker
**Objective**: Create a CI pipeline using Docker

```groovy
pipeline {
    agent {
        docker { 
            image 'python:3.9' 
            args '-u root'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                sh 'python -m pytest tests/'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.build('myapp:${BUILD_NUMBER}')
                    docker.push()
                }
            }
        }
    }
}
```

**Tasks**:
1. Set up Jenkins with Docker plugin
2. Create a CI pipeline
3. Automate build, test, and deployment processes

## Additional Challenges
1. Containerize a legacy application
2. Implement blue-green deployment
3. Create a microservices architecture
4. Develop a monitoring solution for containers
## Advanced Topics

### Docker Storage Types
- **Volume Storage**: Persistent data management
- **Bind Mounts**: Mapping host filesystem to container
- **Tmpfs Mounts**: Temporary filesystem storage

### Docker Networking
```bash
# Create a network
docker network create my-network

# Connect container to network
docker network connect my-network my-container
```

### Docker Compose Example
```yaml
version: '3'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  database:
    image: postgres:12
    environment:
      POSTGRES_PASSWORD: example
```

## Continuous Integration

### Jenkins Pipeline Example
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t my-app .'
            }
        }
        stage('Test') {
            steps {
                sh 'docker run my-app test'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker push my-registry/my-app'
            }
        }
    }
}
```

## Learning Resources
- [Official Docker Documentation](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)

## Homework Assignments
1. Create a Dockerfile for a simple web application
2. Push a container to Docker Hub
3. Set up a multi-container application using Docker Compose
4. Create a basic Jenkins pipeline for continuous integration

## Assessment
- Practical Docker command usage
- Dockerfile creation
- Docker Hub repository management
- Basic container orchestration understanding

## Troubleshooting
- Ensure Docker is properly installed
- Check Docker daemon status
- Verify network configurations
- Review Docker logs for debugging
