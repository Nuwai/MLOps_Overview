# 📘 Chapter 6 — CI/CD for ML (GitHub Actions)

CI/CD is essential in MLOps because ML systems must be **consistent, repeatable, automated**, and **continuously deployable**.  
With GitHub Actions, we can automate:

- Testing  
- Model packaging  
- Docker image builds  
- Deployment to servers or cloud  
- Versioning  
- Environment checks  

This chapter covers everything needed to build a **complete CI/CD pipeline for ML models**.

---

# 🔧 6.1 What Is CI/CD?

## **CI — Continuous Integration**
Automatically:
- Runs tests  
- Checks code formatting  
- Validates environment  
- Builds artifacts  

Triggered on:
- Pull requests  
- Push to main branch  

## **CD — Continuous Delivery / Deployment**
Automatically:
- Builds Docker images  
- Pushes to registry  
- Deploys to server/Kubernetes/Cloud  

### Why ML needs CI/CD:
- Prevents model API failures  
- Automates deployment  
- Ensures reproducibility  
- Supports versioning  
- Reduces risk of manual errors  

---

# 🔁 6.2 Why CI/CD is Important in MLOps

Traditional software:
- Code changes → build + deploy

Machine Learning:
- **Code changes**  
- **Model changes**  
- **Data changes**  
- **Pipeline changes**  

CI/CD allows you to:
- Test your training pipeline  
- Test your inference API  
- Deploy new model versions safely  
- Automate retraining workflows  

---

# 🛠️ 6.3 GitHub Actions Overview

GitHub Actions uses **workflows**, defined in `.github/workflows/*.yml`.

Basic structure:

```yaml
name: Example Workflow

on:
  push:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
```

Workflow triggers:
- `push`  
- `pull_request`  
- `schedule` (cron jobs)  
- `workflow_dispatch` (manual run)  

---

# 🧪 6.4 Automated Testing for ML

We test:
- Data loaders  
- Preprocessing  
- Model inference  
- API endpoints  
- Training pipeline  

Example test file (using pytest):

```python
def test_prediction_shape(model):
    pred = model.predict([[1,2,3,4]])
    assert len(pred) == 1
```

Add to CI:

```yaml
- name: Run tests
  run: pytest
```

---

# 🐳 6.5 Automating Docker Builds

CI can build Docker images for you:

```yaml
- name: Build Docker image
  run: docker build -t myapi:latest .
```

Push to registry:

```yaml
- name: Login to DockerHub
  uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKER_USER }}
    password: ${{ secrets.DOCKER_PASS }}

- name: Push image
  run: docker push myrepo/myapi:latest
```

---

# 🚀 6.6 Full CI Pipeline (Build + Test + Lint)

A typical ML CI workflow:

```yaml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.10'

    - name: Install dependencies
      run: pip install -r requirements.txt

    - name: Run tests
      run: pytest

    - name: Lint
      run: flake8 .
```

---

# 📦 6.7 CD: Deployment Pipeline

When CI passes, CD deploys automatically.

Example: deploy to server via SSH:

```yaml
- name: Deploy via SSH
  uses: appleboy/ssh-action@v1.0.0
  with:
    host: ${{ secrets.SERVER_IP }}
    username: ubuntu
    key: ${{ secrets.SERVER_SSH_KEY }}
    script: |
      docker pull myrepo/mlapi:latest
      docker stop mlapi || true
      docker rm mlapi || true
      docker run -d -p 80:80 --name mlapi myrepo/mlapi:latest
```

---

# 🚢 6.8 CI/CD Pipeline for FastAPI ML Deployment

```
Git Push → GitHub Actions → Build Docker Image → Push to Registry → Deploy to Server → API Live
```

### Diagram

```
Developer
   │
   ▼
GitHub Repo
   │
   ▼
GitHub Actions CI Workflow
   │
   ├─ Run tests
   ├─ Build Docker image
   ├─ Push to registry
   ▼
CD Deployment Workflow
   │
   ▼
Production Server / Kubernetes
```

---

# 🖥️ 6.9 Versioning Strategies

### 1. Semantic Versioning
```
v1.0.0
v1.1.0
v2.0.0
```

### 2. Model Versioning inside registry
```
my_model:v1
my_model:v2
```

### 3. Git-Based Versioning
- Git hash  
- Commit tags  
- GitHub releases  

---

# 💡 6.10 Example: Build & Push Docker Image (Complete Workflow)

```yaml
name: Build and Push

on:
  push:
    branches: ["main"]

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Login to DockerHub
      uses: docker/login-action@v2
      with:
        username: ${{ secrets.DOCKER_USER }}
        password: ${{ secrets.DOCKER_PASS }}

    - name: Build Image
      run: docker build -t myrepo/mlapi:latest .

    - name: Push Image
      run: docker push myrepo/mlapi:latest
```

---

# 🌐 6.11 Example: Deploy FastAPI App to Server

```yaml
name: Deploy API

on:
  push:
    branches: ["main"]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: SSH Deploy
      uses: appleboy/ssh-action@v1.0.0
      with:
        host: ${{ secrets.SERVER_IP }}
        username: ubuntu
        key: ${{ secrets.SERVER_SSH_KEY }}
        script: |
          docker pull myrepo/mlapi:latest
          docker stop mlapi || true
          docker rm mlapi || true
          docker run -d -p 80:80 myrepo/mlapi:latest
```

---

# 🚦 6.12 Adding Health Checks in CI

CI can call your API endpoint during tests:

```yaml
- name: Check health endpoint
  run: curl http://localhost:8000/health
```

---

# 🧪 6.13 Adding Cron-Based Retraining Pipelines

Example:

```yaml
on:
  schedule:
    - cron: "0 3 * * *"  # daily at 3 AM
```

Runs:
- Retraining script  
- Model evaluation  
- Deployment  

---

# 🧹 6.14 Common CI/CD Mistakes in ML

❌ Not testing model inference  
❌ Pushing broken Docker images  
❌ No environment variables  
❌ Storing large datasets in repo  
❌ Failing to pin package versions  
❌ Manual deployments  

---

# 📌 6.15 Real-World CI/CD Setup for ML (Companies Use This)

```
git push main
      │
      ▼
GitHub Actions CI
      │
      ├─ lint
      ├─ unit tests
      ├─ integration tests
      ├─ build + tag docker image
      └─ push to registry
      ▼
GitHub Actions CD
      │
      ├─ pull image on server
      ├─ restart container
      └─ run smoke tests
      ▼
 Production
```

---

# 🧪 6.16 Exercises

### **Exercise 1**
Create a GitHub Actions workflow that:
- Installs dependencies  
- Runs tests  
- Builds Docker image  

### **Exercise 2**
Modify the workflow to push to DockerHub.

### **Exercise 3**
Create a deployment workflow using SSH or Kubernetes.

---

# 📝 6.17 Summary

- CI ensures code & model quality  
- CD automates deployment  
- Docker + GitHub Actions = production-ready MLOps  
- Use secrets for sensitive data  
- Use workflows for:
  - testing  
  - building  
  - tagging  
  - pushing  
  - deploying  

---

