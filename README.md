# Understanding DevOps: Bridging Development and Operations  

If you’ve ever built an app and thought:  

- How do I make sure it runs the same everywhere?  
- How can I test changes automatically?  
- How do I go from my laptop to production safely?  

…then this guide is for you.  

In this **hands-on tutorial**, we’ll set up a complete DevOps pipeline, starting with a simple **Node.js app**, then moving to **automated CI/CD deployments with Docker and Kubernetes**.  

By the end, you will know how to:  
- ⚡ Set up Git, Docker, and GitHub Actions  
- 🧪 Write and run automated tests  
- 📦 Build lightweight, secure Docker images  
- 🤖 Use CI/CD pipelines for testing, building, and deploying automatically  
- 🌍 Deploy to staging & production with confidence  

> 💡 **No prior DevOps experience required** — just curiosity, code, and a willingness to learn!  

---

# 🛠 Hands-On Practice: Preparing Your DevOps Workspace  

## 🔧 Before You Begin: Install Required Tools  

1. **Node.js (v18 or higher, LTS v20 recommended in 2025)**  
   - [Download here](https://nodejs.org/)  
   - Verify installation:  
     ```bash
     node --version
     npm --version
     ```

2. **Git (latest stable version)**  
   - [Download here](https://git-scm.com/downloads)  
   - Verify installation:  
     ```bash
     git --version
     ```

3. **Docker Desktop (latest version)**  
   - [Download here](https://www.docker.com/products/docker-desktop/)  
   - Verify installation:  
     ```bash
     docker --version
     docker-compose --version
     ```

4. **GitHub Account** → [Sign up here](https://github.com)  

5. **Code Editor (recommended)** → [Visual Studio Code](https://code.visualstudio.com/)  

✅ **Final Check** — Run these:  

```bash
node --version    # v18.x+ or v20.x+
npm --version     # v9.x+ or v10.x+
git --version     # v2.34+
docker --version  # v24.x+
```

Project Structure

```bash
devops-project/
├── app.js
├── package.json
├── Dockerfile
├── docker-compose.yml
├── jest.config.js
├── .eslintrc.js
├── .dockerignore
├── .gitignore
├── .env.example
├── tests/
│   └── app.test.js
├── k8s/
│   ├── staging/
│   │   └── deployment.yml
│   └── production/
│       └── deployment.yml
└── .github/workflows/
    └── ci.yml
```

### Step 1: Set Up Git for Version Control
Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

Initialize project:

```bash
mkdir devops-project
cd devops-project
git init
```

### Step 2: Build a Node.js Web App
#### 1. Initialize Node.js Project

```bash
npm init -y
```

#### 2. Update package.json
Add scripts, metadata, and dev tools:

```json
{
  "name": "my-devops-project",
  "version": "1.0.0",
  "description": "DevOps learning project with Node.js",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "test": "jest",
    "dev": "node app.js",
    "lint": "eslint ."
  },
  "keywords": ["devops", "nodejs", "docker"],
  "author": "Your Name",
  "license": "MIT",
  "engines": {
    "node": ">=18.0.0"
  },
  "devDependencies": {
    "jest": "^29.7.0",
    "eslint": "^8.57.0",
    "supertest": "^7.1.4"
  }
}
```

#### 3. Create app.js

```js
const http = require('http');
const url = require('url');
const port = process.env.PORT || 3000;

let requestCount = 0;

const server = http.createServer((req, res) => {
  requestCount++;
  const { pathname } = url.parse(req.url, true);

  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('X-Content-Type-Options', 'nosniff');

  switch (pathname) {
    case '/':
      res.writeHead(200, { 'Content-Type': 'text/html' });
      res.end(`<h1>I'm Getting Better at DevOps, Yay!</h1>`);
      break;

    case '/health':
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ status: 'healthy', uptime: process.uptime() }));
      break;

    case '/info':
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ platform: process.platform, pid: process.pid }));
      break;

    case '/metrics':
      res.writeHead(200, { 'Content-Type': 'text/plain' });
      res.end(`http_requests_total ${requestCount}`);
      break;

    default:
      res.writeHead(404, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ error: 'Not Found' }));
  }
});

server.listen(port, () => {
  console.log(`🚀 Server running at http://localhost:${port}/`);
});

module.exports = server;
```

### Install dependencies:

```bash
npm install --save-dev jest eslint supertest
npm install
```

### Step 3: Create Proper Tests
Create a tests/app.test.js file:

```js
const request = require('supertest');
const server = require('../app');

describe('App Endpoints', () => {
  afterAll(() => server.close());

  test('GET / should return homepage', async () => {
    const res = await request(server).get('/');
    expect(res.status).toBe(200);
    expect(res.text).toContain('DevOps');
  });

  test('GET /health should return healthy', async () => {
    const res = await request(server).get('/health');
    expect(res.status).toBe(200);
    expect(res.body.status).toBe('healthy');
  });

  test('GET /info should return system info', async () => {
    const res = await request(server).get('/info');
    expect(res.status).toBe(200);
    expect(res.body.platform).toBeDefined();
  });

  test('GET /metrics should return metrics', async () => {
    const res = await request(server).get('/metrics');
    expect(res.status).toBe(200);
    expect(res.text).toContain('http_requests_total');
  });

  test('GET /nonexistent should return 404', async () => {
    const res = await request(server).get('/nonexistent');
    expect(res.status).toBe(404);
    expect(res.body.error).toBe('Not Found');
  });
});
```

#### Add Jest config jest.config.js:

```js
module.exports = {
  testEnvironment: 'node',
  collectCoverage: true,
  coverageDirectory: 'coverage',
  testMatch: ['**/tests/**/*.test.js']
};
```

#### Run tests:

```bash
npm test
```

### Step 4: GitHub Actions for CI/CD
Create .github/workflows/ci.yml:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm ci
      - run: npm test
```

🐳 Step 5: Dockerfile
```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:20-alpine
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY app.js ./
USER node
EXPOSE 3000
CMD ["node", "app.js"]
```

### Step 6: Config Files
.dockerignore:

```bash
node_modules
.git
coverage
.env
```

.gitignore:

```bash
node_modules/
.env
coverage/
```

.env.example:

```ini
Copy code
PORT=3000
NODE_ENV=production
```

.eslintrc.js:

```js
module.exports = { extends: ['eslint:recommended'] };
```

Step 7: Docker Compose

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - PORT=3000
```

### Run:

```bash
docker-compose up -d
```

## Step 8: Running Everything
Run locally:

```bash
npm install
npm test
npm start
```

Run with Docker:

```bash
docker build -t my-devops-app .
docker run -p 3000:3000 my-devops-app
```

Run with Compose:

```bash
docker-compose up -d
```

## Step 9–11: CI/CD + Deployment

Push to develop → Deploys to staging

Push to main → Deploys to production

GitHub Actions automates tests, builds, scans, and deployments

Kubernetes configs for staging & production included in k8s/.

Conclusion
At this point, you now have:

A working Node.js app

Automated tests with Jest

A CI/CD pipeline with GitHub Actions

Docker + Compose for containerization

Kubernetes configs for scalable deployments

Congratulations! You’ve built a full DevOps pipeline from code → CI → Docker → Kubernetes → CD 

