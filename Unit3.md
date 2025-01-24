# Docker Unit 3: Microservices Practical Exercises

## Exercise 1: Simple Microservices Blog Platform

### Project Structure
```
blog-microservices/
│
├── user-service/
│   ├── Dockerfile
│   ├── package.json
│   └── index.js
│
├── post-service/
│   ├── Dockerfile
│   ├── package.json
│   └── index.js
│
└── docker-compose.yml
```

### Step 1: User Service Implementation
```javascript
// user-service/index.js
const express = require('express');
const app = express();
const PORT = 3001;

const users = [
  { id: 1, username: 'john_doe', email: 'john@example.com' },
  { id: 2, username: 'jane_smith', email: 'jane@example.com' }
];

app.get('/users', (req, res) => {
  res.json(users);
});

app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (user) {
    res.json(user);
  } else {
    res.status(404).json({ message: 'User not found' });
  }
});

app.listen(PORT, () => {
  console.log(`User service running on port ${PORT}`);
});
```

### Step 2: Post Service Implementation
```javascript
// post-service/index.js
const express = require('express');
const app = express();
const PORT = 3002;

const posts = [
  { id: 1, userId: 1, title: 'First Post', content: 'Hello World!' },
  { id: 2, userId: 2, title: 'Docker Microservices', content: 'Learning microservices...' }
];

app.get('/posts', (req, res) => {
  res.json(posts);
});

app.get('/posts/user/:userId', (req, res) => {
  const userPosts = posts.filter(p => p.userId === parseInt(req.params.userId));
  res.json(userPosts);
});

app.listen(PORT, () => {
  console.log(`Post service running on port ${PORT}`);
});
```

### Step 3: Dockerfiles
```dockerfile
# user-service/Dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3001
CMD ["node", "index.js"]

# post-service/Dockerfile
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3002
CMD ["node", "index.js"]
```

### Step 4: Docker Compose Configuration
```yaml
version: '3.8'
services:
  user-service:
    build: ./user-service
    ports:
      - "3001:3001"
    networks:
      - blog-network

  post-service:
    build: ./post-service
    ports:
      - "3002:3002"
    networks:
      - blog-network

networks:
  blog-network:
    driver: bridge
```

### Step 5: Package Configuration
```json
// user-service/package.json
{
  "name": "user-service",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.17.1"
  }
}

// post-service/package.json
{
  "name": "post-service",
  "version": "1.0.0",
  "dependencies": {
    "express": "^4.17.1"
  }
}
```

### Exercise Commands
```bash
# Project Setup
mkdir blog-microservices
cd blog-microservices

# Create services
mkdir user-service post-service

# Create files in respective directories
# (Use the code provided above)

# Install dependencies
cd user-service
npm init -y
npm install express
cd ../post-service
npm init -y
npm install express

# Create Docker Compose file
cd ..
touch docker-compose.yml

# Build and Run Services
docker-compose build
docker-compose up -d

# Verify Services
docker-compose ps
curl http://localhost:3001/users
curl http://localhost:3002/posts
```

## Exercise 2: Service Communication

### API Gateway Implementation
```javascript
// api-gateway/index.js
const express = require('express');
const axios = require('axios');
const app = express();
const PORT = 8080;

app.get('/user-posts/:userId', async (req, res) => {
  try {
    const userId = req.params.userId;
    
    // Fetch user details
    const userResponse = await axios.get(`http://user-service:3001/users/${userId}`);
    
    // Fetch user's posts
    const postsResponse = await axios.get(`http://post-service:3002/posts/user/${userId}`);
    
    res.json({
      user: userResponse.data,
      posts: postsResponse.data
    });
  } catch (error) {
    res.status(500).json({ message: 'Error fetching data' });
  }
});

app.listen(PORT, () => {
  console.log(`API Gateway running on port ${PORT}`);
});
```

### Updated Docker Compose
```yaml
version: '3.8'
services:
  user-service:
    build: ./user-service
    networks:
      - blog-network

  post-service:
    build: ./post-service
    networks:
      - blog-network

  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    networks:
      - blog-network
    depends_on:
      - user-service
      - post-service

networks:
  blog-network:
    driver: bridge
```

## Learning Objectives
- [ ] Microservices architecture
- [ ] Docker Compose configuration
- [ ] Inter-service communication
- [ ] Service discovery
- [ ] Container networking

## Troubleshooting
```bash
# View service logs
docker-compose logs -f

# Check network configuration
docker network ls
docker network inspect blog-microservices_blog-network
```
