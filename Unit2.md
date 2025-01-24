# Docker Unit 2: Practical Docker Operations Exercises

## Exercise 1: Docker Hub and Container Management

### Step 1: Docker Hub Account Setup
```bash
# Create Docker Hub account
# Visit hub.docker.com
# Sign up with email
# Verify email address

# Login via terminal
docker login
```

### Step 2: Create Custom Image
```bash
# Create project directory
mkdir docker-demo
cd docker-demo

# Create application files
cat > app.py << EOL
print("Hello from Docker Hub!")
EOL

# Create Dockerfile
cat > Dockerfile << EOL
FROM python:3.9-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
EOL
```

### Step 3: Build and Tag Image
```bash
# Build image
docker build -t mydemo-app:v1 .

# Tag image for Docker Hub
docker tag mydemo-app:v1 yourusername/mydemo-app:v1
```

### Step 4: Push to Docker Hub
```bash
# Push to repository
docker push yourusername/mydemo-app:v1
```

## Exercise 2: Container Modification

### Step 1: Create Base Container
```bash
# Pull Ubuntu image
docker pull ubuntu:20.04

# Run interactive container
docker run -it --name dev-container ubuntu:20.04 /bin/bash
```

### Step 2: Modify Container
```bash
# Inside container
apt-get update
apt-get install -y python3 pip git

# Exit container
exit
```

### Step 3: Commit Changes
```bash
# Commit modified container to new image
docker commit dev-container mydemo-dev:v1

# Verify new image
docker images
```

## Exercise 3: Docker Storage Management

### Step 1: Volume Creation
```bash
# Create named volume
docker volume create mydata

# Verify volume
docker volume ls
docker volume inspect mydata
```

### Step 2: Volume Mounting
```bash
# Run container with volume
docker run -d \
    --name data-container \
    -v mydata:/app/data \
    ubuntu

# Create file in volume
docker exec data-container \
    bash -c "echo 'Hello, Docker Volume!' > /app/data/sample.txt"
```

### Step 3: Volume Backup
```bash
# Backup volume
docker run --rm \
    -v mydata:/data \
    -v $(pwd)/backup:/backup \
    ubuntu tar cvf /backup/mydata-backup.tar /data
```

## Exercise 4: Advanced Container Management

### Step 1: Resource Constraints
```bash
# Run container with resource limits
docker run -d \
    --name limited-container \
    --cpus=0.5 \
    --memory=256m \
    nginx
```

### Step 2: Container Monitoring
```bash
# Monitor container resources
docker stats limited-container
```

## Comprehensive Workflow Commands
```bash
# List all containers
docker ps -a

# Remove all stopped containers
docker container prune

# Remove unused images
docker image prune

# System-wide Docker info
docker system info
```

## Learning Objectives
- [x] Docker Hub workflow
- [x] Container modification
- [x] Volume management
- [x] Resource constraints
- [x] Container lifecycle management

## Troubleshooting
```bash
# View container logs
docker logs <container_id>

# Inspect container details
docker inspect <container_id>
```
