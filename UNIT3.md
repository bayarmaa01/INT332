# Docker Unit 3: Microservices and Docker Compose

## 1. Microservices Introduction

### Example: Blog Platform Architecture
```yaml
# microservices-architecture.yml
services:
  users-service:
    image: users-service:v1
    ports:
      - "3001:3000"

  posts-service:
    image: posts-service:v1
    ports:
      - "3002:3000"

  comments-service:
    image: comments-service:v1
    ports:
      - "3003:3000"

  api-gateway:
    image: api-gateway:v1
    ports:
      - "8080:8080"
    depends_on:
      - users-service
      - posts-service
      - comments-service
```

## 2. Docker Compose Basics

### Complete Blog Platform Docker Compose
```yaml
version: '3.8'
services:
  users-service:
    build: ./users-service
    ports:
      - "3001:3000"
    environment:
      - DB_HOST=users-db
      - PORT=3000

  posts-service:
    build: ./posts-service
    ports:
      - "3002:3000"
    environment:
      - DB_HOST=posts-db
      - PORT=3000

  users-db:
    image: postgres:13
    environment:
      - POSTGRES_DB=users
      - POSTGRES_PASSWORD=secret
    volumes:
      - users-data:/var/lib/postgresql/data

  posts-db:
    image: postgres:13
    environment:
      - POSTGRES_DB=posts
      - POSTGRES_PASSWORD=secret
    volumes:
      - posts-data:/var/lib/postgresql/data

volumes:
  users-data:
  posts-data:
```

## 3. YAML Configuration Deep Dive

### Complex Configuration Example
```yaml
version: '3.8'
services:
  webapp:
    build: 
      context: ./app
      dockerfile: Dockerfile.dev
    ports:
      - "5000:5000"
    volumes:
      - ./app:/app
      - node_modules:/app/node_modules
    environment:
      - NODE_ENV=development
      - PORT=5000
    depends_on:
      - redis
      - mongodb

  redis:
    image: redis:alpine
    ports:
      - "6379:6379"

  mongodb:
    image: mongo:latest
    environment:
      - MONGO_INITDB_DATABASE=myapp
    volumes:
      - mongodb_data:/data/db

volumes:
  node_modules:
  mongodb_data:
```

## 4. Practical Microservices Exercise

### User Management Microservice
```dockerfile
# users-service/Dockerfile
FROM node:14-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Run application
CMD ["npm", "start"]
```

### User Service Implementation
```javascript
// users-service/index.js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

// User endpoints
app.get('/users', (req, res) => {
  res.json([
    { id: 1, name: 'John Doe' },
    { id: 2, name: 'Jane Smith' }
  ]);
});

app.listen(PORT, () => {
  console.log(`Users service running on port ${PORT}`);
});
```

## 5. Docker Compose Commands

```bash
# Start services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down

# Rebuild images
docker-compose build

# Scale services
docker-compose up -d --scale users-service=3
```

## 6. Advanced Networking

### Internal Network Configuration
```yaml
version: '3.8'
services:
  webapp:
    networks:
      - backend
      - frontend

  database:
    networks:
      - backend

networks:
  frontend:
  backend:
```

## Learning Objectives
- [x] Microservices architecture
- [x] Docker Compose configuration
- [x] Service communication
- [x] Network management
- [x] Volume management

## Recommended Tools
- Docker Compose
- Postman
- VS Code
```
