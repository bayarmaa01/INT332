# Docker Unit 1: Introduction to Docker

## Learning Objectives
- Understand Docker fundamentals
- Master Docker installation
- Learn Docker architecture
- Practice basic Docker commands

## 1. Docker Fundamentals

### What is Docker?
Docker is a platform for developing, shipping, and running applications in containers. It provides:
- Consistent environment across development and production
- Lightweight alternative to virtual machines
- Easy scaling and deployment

### Key Components
1. **Docker Engine**: Core runtime environment
2. **Docker Client**: Command-line interface
3. **Docker Daemon**: Background service managing containers
4. **Docker Registry**: Repository for Docker images

## 2. Docker Installation

### Windows Installation
```powershell
# Prerequisites
# - Windows 10 Pro/Enterprise
# - Hyper-V support

# Steps:
# 1. Download Docker Desktop
# 2. Enable Hyper-V in Windows Features
# 3. Install Docker Desktop
# 4. Restart computer

# Verify Installation
docker --version
docker run hello-world
```

### Linux (Ubuntu) Installation
```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up stable repository
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

# Add user to docker group
sudo usermod -aG docker $USER

# Verify installation
docker --version
docker run hello-world
```

## 3. Docker Architecture Practical Exercise

### Create Dockerfile Example
```dockerfile
# Dockerfile: Python Web App
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 5000

# Run application
CMD ["python", "app.py"]
```

### Docker Image Build and Run
```bash
# Build Docker image
docker build -t my-web-app .

# Run container
docker run -p 5000:5000 my-web-app
```

## 4. Basic Docker Commands

### Image Management
```bash
# Pull image
docker pull ubuntu:20.04

# List images
docker images

# Remove image
docker rmi ubuntu:20.04
```

### Container Operations
```bash
# Run interactive container
docker run -it ubuntu /bin/bash

# List running containers
docker ps

# List all containers
docker ps -a

# Stop container
docker stop <container_id>

# Remove container
docker rm <container_id>
```

## 5. Practical Exercises

### Exercise 1: Container Lifecycle
1. Pull Ubuntu image
2. Run interactive container
3. Install packages
4. Exit and restart container

### Exercise 2: Custom Image Creation
1. Write a simple Dockerfile
2. Build custom image
3. Run and test container

### Exercise 3: Docker Networking
```bash
# Create custom network
docker network create my-network

# Run container on custom network
docker run --network=my-network nginx
```

## 6. Common Challenges and Solutions

### Troubleshooting
```bash
# Check Docker service status
sudo systemctl status docker

# View Docker logs
journalctl -u docker.service

# Prune unused resources
docker system prune -a
```
# Docker Practical Exercises with Detailed Steps

## Exercise 1: Basic Container Deployment

### Objective: Deploy a Simple Web Server

**Step-by-Step Instructions**:
1. Open your terminal
2. Verify Docker is installed:
   ```bash
   docker --version
   ```

3. Pull Nginx web server image:
   ```bash
   docker pull nginx:latest
   ```

4. Run Nginx container:
   ```bash
   docker run -d -p 8080:80 --name my-web-server nginx
   ```
   - `-d`: Run in detached mode
   - `-p 8080:80`: Map host port 8080 to container port 80
   - `--name my-web-server`: Name the container

5. Verify container is running:
   ```bash
   docker ps
   ```

6. Open web browser and visit:
   `http://localhost:8080`

7. Stop the container:
   ```bash
   docker stop my-web-server
   ```

## Exercise 2: Database Container Management

### Objective: Deploy MySQL Database

**Step-by-Step Instructions**:
1. Pull MySQL image:
   ```bash
   docker pull mysql:latest
   ```

2. Run MySQL container:
   ```bash
   docker run -d \
     --name my-mysql-db \
     -e MYSQL_ROOT_PASSWORD=mypassword \
     -e MYSQL_DATABASE=myapp \
     -p 3306:3306 \
     mysql:latest
   ```
   - `-e`: Set environment variables
   - `MYSQL_ROOT_PASSWORD`: Set root password
   - `MYSQL_DATABASE`: Create initial database

3. Check container logs:
   ```bash
   docker logs my-mysql-db
   ```

4. Connect to MySQL (if MySQL client installed):
   ```bash
   mysql -h localhost -P 3306 -u root -p
   ```

5. Remove container when done:
   ```bash
   docker stop my-mysql-db
   docker rm my-mysql-db
   ```

## Exercise 3: Custom Dockerfile Creation

### Objective: Build a Custom Python Web App

1. Create project directory:
   ```bash
   mkdir python-webapp
   cd python-webapp
   ```

2. Create `app.py`:
   ```python
   from flask import Flask
   app = Flask(__name__)

   @app.route('/')
   def hello():
       return "Hello, Docker!"

   if __name__ == '__main__':
       app.run(host='0.0.0.0', port=5000)
   ```

3. Create `requirements.txt`:
   ```
   flask
   ```

4. Create `Dockerfile`:
   ```dockerfile
   FROM python:3.9-slim
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY app.py .
   EXPOSE 5000
   CMD ["python", "app.py"]
   ```

5. Build Docker image:
   ```bash
   docker build -t my-python-app .
   ```

6. Run the container:
   ```bash
   docker run -d -p 5000:5000 --name python-web my-python-app
   ```

7. Test application:
   Open `http://localhost:5000`

## Exercise 4: Docker Network Configuration

### Objective: Create Custom Network

1. Create custom network:
   ```bash
   docker network create myapp-network
   ```

2. Run containers on network:
   ```bash
   docker run -d \
     --name web-server \
     --network myapp-network \
     nginx

   docker run -d \
     --name database \
     --network myapp-network \
     mysql:latest
   ```

3. Inspect network:
   ```bash
   docker network inspect myapp-network
   ```

## Exercise 5: Volume Management

### Objective: Persistent Data Storage

1. Create named volume:
   ```bash
   docker volume create mydata
   ```

2. Run container with volume:
   ```bash
   docker run -d \
     --name data-container \
     -v mydata:/data \
     ubuntu
   ```

3. List volumes:
   ```bash
   docker volume ls
   ```

## Troubleshooting Tips
- Use `docker ps -a` to see all containers
- Use `docker logs <container-name>` to view logs
- Restart Docker service if issues occur
## Learning Checklist
- [ ] Docker installation
- [ ] Understand Docker architecture
- [ ] Create Dockerfile
- [ ] Basic image and container management
- [ ] Networking fundamentals

## Additional Resources
- Docker Official Documentation: https://docs.docker.com
- Docker Hub: https://hub.docker.com
```
