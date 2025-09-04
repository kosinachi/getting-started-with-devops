DevOps Lab 2025 – Complete CI/CD Setup

Welcome to DevOps Lab 2025 🎉
This repository walks you through setting up a real-world DevOps environment using Node.js, Docker, GitHub Actions, and Kubernetes — from local development all the way to automated deployments.

It’s designed for beginners and professionals alike who want to understand DevOps concepts through hands-on practice.

📑 Table of Contents

Requirements

Step 1: Git Setup

Step 2: Basic Node.js App

Step 3: Automated Tests

Step 4: GitHub Actions CI/CD

Step 5: Dockerfile

Step 6: Essential Configs

Step 7: Docker Compose

Step 8: Run Everything

Step 9: Push to GitHub

Step 10: Continuous Deployment

Step 11: Branch Strategy

Step 12: Conclusion & Best Practices

🔧 Requirements

Install these tools before starting:

Node.js (v18+ or v20 LTS) → Download here

node --version
npm --version


Git → Download here

git --version


Docker Desktop → Download here

docker --version
docker-compose --version


GitHub Account → Sign up

Code Editor (VS Code recommended) → Download here

📝 Step 1: Git Setup

Configure Git and initialize your project:

git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main

mkdir my-devops-project
cd my-devops-project
git init

⚡ Step 2: Basic Node.js App

Create a simple app.js file with a web server (homepage, health check, metrics, etc.).
This will be the base of your DevOps pipeline.

✅ Step 3: Automated Tests

Set up Jest + Supertest:

mkdir tests
touch tests/app.test.js


Add tests in tests/app.test.js to check endpoints like /, /health, /info, /metrics.

Create Jest config:

touch jest.config.js

module.exports = {
  testEnvironment: 'node',
  collectCoverage: true,
  coverageDirectory: 'coverage',
  testMatch: ['**/tests/**/*.test.js'],
  verbose: true
};

🔄 Step 4: GitHub Actions CI/CD

Create .github/workflows/ci.yml with a pipeline that:

Runs tests

Builds Docker images

Pushes to GitHub Container Registry

👉 See ci.yml
 in this repo.

📦 Step 5: Dockerfile

Use a multi-stage Dockerfile for smaller, secure images:

Non-root user for security

Health checks configured

Optimized caching

👉 See Dockerfile
.

⚙️ Step 6: Essential Configs

.dockerignore → Ignore unnecessary files in Docker builds

.gitignore → Ignore temp/dependency files in Git

.env.example → Template for environment variables

.eslintrc.js → Enforce coding standards

🐳 Step 7: Docker Compose

Run app + services together with one command:

touch docker-compose.yml


Maps port 3000:3000, sets env vars, adds health check.

🖥️ Step 8: Run Everything

Run locally:

npm install
npm test
npm start


Test endpoints:

curl http://localhost:3000/
curl http://localhost:3000/health
curl http://localhost:3000/info
curl http://localhost:3000/metrics


Run with Docker:

docker build -t my-devops-app:latest .
docker run -d -p 3000:3000 my-devops-app:latest


Run with Docker Compose:

docker-compose up -d

🌐 Step 9: Push to GitHub
git add .
git commit -m "Initial commit: Complete DevOps setup with working CI/CD"
git branch -M main
git remote add origin https://github.com/yourusername/my-devops-project.git
git push -u origin main

🚀 Step 10: Continuous Deployment

Enhanced pipeline:

Deploys to staging on develop branch

Deploys to production on main branch

Runs Trivy security scans

👉 See .github/workflows/ci.yml.

🏗️ Step 11: Branch Strategy

develop → Deploys to staging automatically

main → Deploys to production automatically

Pull requests → Run tests only

🏆 Step 12: Conclusion & Best Practices

Keep CI/CD fast

Automate security scans

Protect secrets (.env, GitHub secrets)

Monitor health + metrics

Use branching wisely

📚 Resources

Node.js

Docker

GitHub Actions

Kubernetes

👨‍💻 Author

Built with ❤️ by Your Name
