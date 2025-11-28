# 📘 Chapter 5 — Docker for MLOps (Very Detailed)

Docker is one of the **most important tools in MLOps**.  
It ensures your ML model runs **exactly the same** on your laptop, in CI/CD, on a server, or in a Kubernetes cluster.

This chapter covers:

- What containers are  
- Images vs containers  
- Docker layers  
- Dockerfile deep dive  
- Slim images  
- GPU Docker images  
- Docker Compose  
- Exposing FastAPI ports  
- Building + publishing images  
- Best practices for ML  
- Full working Dockerfiles  

---

# 🧩 5.1 What Are Containers?

A **container** packages:

- Code  
- Dependencies  
- OS libraries  
- Environment variables  

Into a portable, isolated unit.

### Analogy:
> A Docker container is like a **shipping container**:  
> same shape, same interface, same behavior everywhere.

### Why ML needs Docker
- Reproducible environments  
- Avoid “works on my machine”  
- Support GPU environments  
- Deployment-ready packages  

---

# 📦 5.2 Docker Images vs Containers

| Term | Meaning |
|------|---------|
| **Image** | Template (read-only blueprint) |
| **Container** | Running instance of the image |

### Diagram

```
Dockerfile → Image → Container
```

---

# 🧱 5.3 Docker Layers

A Docker image is built from **layers**.

Example:

```
FROM python:3.10
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app/ .
```

Each instruction = one layer.  
Layers are cached → massive performance boost.

---

# 📝 5.4 Deep Dive: Dockerfile for ML

Here is a simple but correct Dockerfile for FastAPI + ML:

```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

---

# 📌 5.5 Best Practices for ML Dockerfiles

### ✅ Use slim images

```
python:3.10-slim
```

Slim = smaller image size → faster pulls → cheaper deployment.

### ✅ Use `.dockerignore`

```
__pycache__/
data/
models/
*.ipynb
```

### ✅ Install Python deps efficiently

```
pip install --no-cache-dir -r requirements.txt
```

### ✅ Avoid copying entire project early

This breaks caching.

### ❌ Bad

```
COPY . .
RUN pip install -r requirements.txt
```

### ✔️ Good

```
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
```

---

# ⚙️ 5.6 GPU Docker Images

For deep learning, use **NVIDIA CUDA images**:

```dockerfile
FROM nvidia/cuda:12.1.0-runtime-ubuntu22.04
```

Install Python manually:

```bash
apt-get update && apt-get install python3 python3-pip -y
```

### Running with GPU:

```bash
docker run --gpus all myimage
```

---

# 🧪 5.7 Exposing FastAPI Ports

FastAPI default:

```bash
uvicorn main:app --host 0.0.0.0 --port 80
```

Expose the port:

```dockerfile
EXPOSE 80
```

Run container:

```bash
docker run -p 8000:80 myimage
```

Now your API is available at:

```
http://localhost:8000
```

---

# 🗂️ 5.8 Docker Compose for MLOps

Useful for multi-container systems:

- API  
- Database  
- Redis  
- Message broker  
- ML workers  

### Example `docker-compose.yml`:

```yaml
version: "3"

services:
  api:
    build: .
    ports:
      - "8000:80"
    environment:
      - MODEL_PATH=models/model.pkl
```

Run:

```bash
docker compose up --build
```

---

# 🛠️ 5.9 Building Docker Images

```bash
docker build -t ml-api .
```

### Tagging images

```bash
docker tag ml-api myrepo/ml-api:v1
```

---

# ☁️ 5.10 Publishing to Docker Hub

Login:

```bash
docker login
```

Push:

```bash
docker push myrepo/ml-api:v1
```

---

# 🧪 5.11 Full ML Dockerfile (Optimized)

```
# Step 1 — Base image
FROM python:3.10-slim

# Step 2 — Work directory
WORKDIR /app

# Step 3 — System deps (optional)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential

# Step 4 — Install Python deps
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Step 5 — Copy application
COPY . .

# Step 6 — Expose port
EXPOSE 80

# Step 7 — Start server
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

---

# 🌀 5.12 Example: Dockerfile for GPU ML API

```
FROM nvidia/cuda:12.1.0-runtime-ubuntu22.04

RUN apt-get update && apt-get install -y python3 python3-pip

WORKDIR /app

COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 80

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```

Run with GPU:

```bash
docker run --gpus all -p 8000:80 myimage
```

---

# 🏗️ 5.13 Real Production Architecture Using Docker

```
                 ┌───────────────┐
                 │   CI/CD        │
                 └───────┬────────┘
                         ▼
                Build Docker Image
                         ▼
                Push to Container Registry
                         ▼
              ┌────────────────────────┐
              │     Deployment Target   │
              │  (ECS, Cloud Run, K8s)  │
              └───────────┬────────────┘
                          ▼
                   Run Container
```

---

# 📌 5.14 Common Docker Mistakes in ML

❌ Installing heavy libs inside container  
❌ Copying entire repo before installing dependencies  
❌ Using full `python:3.10` instead of `python:3.10-slim`  
❌ Loading the ML model on every request  
❌ Storing large datasets inside container  

---

# 🧪 5.15 Exercises

### **Exercise 1**
Write a Dockerfile for a FastAPI churn prediction service.

### **Exercise 2**
Modify your Dockerfile to support GPU inference.

### **Exercise 3**
Create a `docker-compose.yml` with:
- API  
- Redis  
- Background worker  

### **Exercise 4**
Optimize your Dockerfile using slim images and `.dockerignore`.

---

# 📝 5.16 Summary

- Docker ensures reproducible ML deployment  
- Images = templates; containers = running instances  
- Slim images reduce bloat  
- Use Docker Compose for multi-service stacks  
- GPU inference uses NVIDIA CUDA images  
- ML Docker best practices make deployments faster and cheaper  

---

