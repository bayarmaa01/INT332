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
