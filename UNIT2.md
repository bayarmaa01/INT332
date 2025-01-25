# Docker Unit 2: Docker Operations with Practical Examples

## 1. Docker Hub Workflow

### Example: Web Application Deployment Workflow
```bash
# 1. Login to Docker Hub
docker login

# 2. Create a sample web application
mkdir web-app
cd web-app

# 3. Create Dockerfile
cat > Dockerfile << EOL
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/
EXPOSE 80
EOL

# 4. Create sample index.html
cat > index.html << EOL
<!DOCTYPE html>
<html>
<body>
    <h1>My Docker Web App</h1>
    <p>Deployed using Docker Hub</p>
</body>
</html>
EOL

# 5. Build Docker image
docker build -t mywebapp:v1 .

# 6. Tag image for Docker Hub
docker tag mywebapp:v1 yourusername/mywebapp:v1

# 7. Push to Docker Hub
docker push yourusername/mywebapp:v1
```

## 2. Container Modification Techniques

### Example: Development Environment Container
```dockerfile
# Development Dockerfile
FROM ubuntu:20.04

# Set environment variables
ENV DEV_HOME=/workspace
ENV LANG=en_US.UTF-8

# Install development tools
RUN apt-get update && apt-get install -y \
    git \
    curl \
    python3 \
    python3-pip \
    nodejs \
    npm

# Set working directory
WORKDIR $DEV_HOME

# Create volume for persistent development
VOLUME $DEV_HOME

# Default command
CMD ["/bin/bash"]
```

### Build and Run Development Container
```bash
# Build development image
docker build -t dev-environment:latest .

# Run interactive development container
docker run -it \
    -v $(pwd):/workspace \
    -p 3000:3000 \
    dev-environment:latest
```

## 3. Docker Storage Management

### Example: Database Persistent Storage
```bash
# Create named volume for PostgreSQL
docker volume create postgres-data

# Run PostgreSQL with persistent storage
docker run -d \
    --name postgres-db \
    -e POSTGRES_PASSWORD=mysecretpassword \
    -v postgres-data:/var/lib/postgresql/data \
    -p 5432:5432 \
    postgres:13
```

### Backup Volume Data
```bash
# Create backup of volume
docker run --rm \
    -v postgres-data:/dbdata \
    -v $(pwd)/backup:/backup \
    ubuntu tar cvf /backup/backup.tar /dbdata
```

## 4. Advanced Container Operations

### Example: Microservices Setup
```yaml
# docker-compose.yml for microservices
version: '3'
services:
  users-service:
    image: users-service:v1
    ports:
      - "3001:3000"
    environment:
      - DB_HOST=postgres
      - PORT=3000

  posts-service:
    image: posts-service:v1
    ports:
      - "3002:3000"
    environment:
      - DB_HOST=postgres
      - PORT=3000

  postgres:
    image: postgres:13
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=mysecretpassword

volumes:
  postgres-data:
```

### Run Microservices
```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Scale services
docker-compose up -d --scale users-service=3
```

## 5. Security and Best Practices

### Image Scanning Example
```bash
# Install Docker Scan
docker scan --version

# Scan image for vulnerabilities
docker scan mywebapp:v1 \
    --severity=high \
    --file=Dockerfile
```

### Resource Constraint Example
```bash
# Run container with limited resources
docker run -d \
    --cpus=0.5 \
    --memory=256m \
    --name limited-app \
    mywebapp:v1
```
# Docker Practical Exercises

## Exercise 1: Docker Hub Account and Image Management

### Part A: Docker Hub Account Creation
1. Visit [Docker Hub](https://hub.docker.com/)
2. Click "Sign Up"
3. Enter email, username, and password
4. Verify email address

### Part B: Docker Image Pushing
```bash
# Login to Docker Hub
docker login

# Pull an example image
docker pull ubuntu:latest

# Tag the image with your Docker Hub username
docker tag ubuntu:latest yourusername/ubuntu:v1

# Push image to Docker Hub
docker push yourusername/ubuntu:v1
```

## Exercise 2: Saving Container Changes

### Method 1: Commit Container Changes
```bash
# Start a container and make changes
docker run -it ubuntu:latest

# Inside container, make modifications
apt-get update
apt-get install -y python3

# Open new terminal, find container ID
docker ps

# Commit changes to a new image
docker commit <container_id> yourusername/ubuntu-python:v1

# Push to Docker Hub
docker push yourusername/ubuntu-python:v1
```

### Method 2: Export and Import Container
```bash
# Export running container to tar file
docker export <container_id> > container.tar

# Import tar file as new image
docker import container.tar yourusername/custom-image:v1
```

## Exercise 3: Dockerfile Creation

### Basic Dockerfile Example
```dockerfile
# Base image
FROM ubuntu:latest

# Metadata
LABEL maintainer="your-email@example.com"

# Update system
RUN apt-get update && apt-get upgrade -y

# Install packages
RUN apt-get install -y python3 pip

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose port
EXPOSE 8000

# Define startup command
CMD ["python3", "app.py"]
```

### Build and Run Dockerfile
```bash
# Build Docker image
docker build -t myapp:v1 .

# Run the container
docker run -p 8000:8000 myapp:v1
```

## Exercise 4: Docker Storage Types

### Volume Storage
```bash
# Create a named volume
docker volume create mydata

# Run container with volume
docker run -v mydata:/app/data ubuntu

# List volumes
docker volume ls

# Inspect volume
docker volume inspect mydata
```

### Bind Mount
```bash
# Mount host directory to container
docker run -v /host/path:/container/path ubuntu
```

### Tmpfs Mount
```bash
# Create temporary filesystem mount
docker run --tmpfs /temp ubuntu
```

## Exercise 5: Container Linking

### Network-Based Linking
```bash
# Create custom network
docker network create myapp-network

# Run database container
docker run -d --name database --network myapp-network postgres

# Run application container linked to database
docker run -d --name webapp --network myapp-network \
  -e DATABASE_URL=postgres://database:5432 mywebapp
```

## Bonus Challenge
1. Create a multi-stage Dockerfile
2. Implement persistent storage for a database
3. Set up a multi-container application using networks
4. Create a custom Docker volume plugin

## Troubleshooting Tips
- Use `docker logs` to debug container issues
- Check network configurations
- Verify volume mounts
- Use `docker inspect` for detailed container information
## Learning Checklist
- [x] Docker Hub workflow
- [x] Container modification
- [x] Persistent storage management
- [x] Microservices deployment
- [x] Security scanning
- [x] Resource management

## Recommended Resources
- Docker Official Documentation
- Docker Hub
- Docker Compose Guide
```
