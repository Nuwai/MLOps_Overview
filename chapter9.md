# 📘 Chapter 9 — Cloud Deployment for ML (AWS, GCP, Azure)

Deploying ML models to the cloud is a core part of modern MLOps.  
This chapter covers real-world deployment patterns using:

- AWS EC2  
- AWS ECS  
- AWS Lambda (Serverless ML)  
- Google Cloud Run  
- Azure App Service  
- Nginx reverse proxy  
- Load balancing  
- Auto-scaling  
- Horizontal vs vertical scaling  

This is a **practical, production-ready guide** for ML cloud deployment.

---

# ☁️ 9.1 Why Deploy Models to the Cloud?

Cloud deployment enables:

- Scalability  
- Reliability  
- High availability  
- Auto scaling  
- Secure access  
- CI/CD integration  
- GPU support  
- Cheaper, optimized compute  

Machine learning services must handle:
- Traffic spikes  
- Large payloads  
- Heavy models  
- Batch jobs  
- Real-time inference  

Cloud services provide infrastructure to manage all of these.

---

# 🏗️ 9.2 Deployment Architecture Overview

```
               ┌──────────────────────────┐
               │    FastAPI ML Service    │
               └───────────┬──────────────┘
                           ▼
               ┌──────────────────────────┐
               │    Docker Container       │
               └───────────┬──────────────┘
                           ▼
          ┌───────────────────────────────┐
          │ Cloud Deployment (EC2/ECS/Run)│
          └──────────┬────────────────────┘
                     ▼
              ┌─────────────┐
              │ Load Balancer│
              └─────────────┘
```

---

# 🖥️ 9.3 Deploying to AWS EC2 (Virtual Machine)

This is the simplest cloud deployment method.

## **Steps:**

### 1️⃣ Launch EC2 instance
- Choose Ubuntu or Amazon Linux  
- Assign public IP  
- Add inbound rule for port 80  

### 2️⃣ Install Docker

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### 3️⃣ Pull Docker image

```bash
docker pull myrepo/mlapi:latest
```

### 4️⃣ Run container

```bash
docker run -d -p 80:80 myrepo/mlapi:latest
```

Your API is now live at:

```
http://EC2_PUBLIC_IP/
```

---

# 🐳 9.4 Deploying to AWS ECS (Elastic Container Service)

ECS is a container orchestrator (simpler than Kubernetes).

## Architecture:

```
Docker Image → ECR → ECS Cluster → Task → Service → Load Balancer
```

### Steps:

1. Push image to ECR  
2. Create ECS cluster  
3. Define Task Definition  
4. Define Service  
5. Attach Load Balancer  
6. Auto-scale tasks  

This setup is production-grade for APIs.

---

# ⚡ 9.5 Deploying ML Models with AWS Lambda (Serverless)

Lambda is **event-driven + serverless**.  
Good for **small ML models** or **light inference workloads**.

### Constraints:
- 15-minute max execution  
- Memory limit  
- CPU limit  
- Cold-start latency  

### When to use:
- Spam detection  
- Quick NLP classification  
- Image thumbnail generation  
- Event-based ML  

### Example Lambda Handler:

```python
import joblib

model = joblib.load("model.pkl")

def lambda_handler(event, context):
    data = event["body"]
    pred = model.predict([data])
    return {"prediction": pred[0]}
```

Package as a zip or container image.

---

# 🌐 9.6 Deploying to Google Cloud Run (Recommended for ML)

Cloud Run is fully managed, serverless container hosting.

### Why ML engineers love it:
- Deploy Docker image  
- Auto scaling  
- Built-in HTTPS  
- Cheap (pay per request)  
- Supports GPUs (new feature)  
- No servers to manage  

### Deploy in one command:

```bash
gcloud run deploy mlapi \
  --image gcr.io/myproject/mlapi:latest \
  --platform managed
```

It returns a URL like:

```
https://mlapi-xyz.a.run.app
```

---

# 🟦 9.7 Deploy to Azure App Service

Steps:

1. Create App Service  
2. Enable Docker container mode  
3. Push image to Azure Container Registry  
4. Point App Service to image  
5. Configure scaling  

This is Azure’s “Cloud Run”.

---

# 🔀 9.8 Reverse Proxy with Nginx

Nginx handles:
- Routing  
- SSL  
- Load balancing  
- Rate limiting  
- Reverse proxy  

### Example `/etc/nginx/sites-available/mlapi.conf`

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
    }
}
```

Enable and restart:

```bash
sudo ln -s /etc/nginx/sites-available/mlapi.conf /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

---

# ⚖️ 9.9 Load Balancing

Load balancers distribute traffic across instances.

AWS:
- **ALB** (Application Load Balancer)

GCP:
- **Cloud Load Balancer**

Azure:
- **Application Gateway**

Improves:
- Availability  
- Performance  
- Fault tolerance  

---

# 📈 9.10 Auto-Scaling ML Services

## Horizontal Scaling (recommended)
Add more instances.

```
Instance × N
```

Works for:
- Many lightweight inference calls  
- Stateless ML APIs  

## Vertical Scaling
Add more CPU/GPU to one machine.

Use when:
- Model is large  
- GPU reliance  

---

# 🏗️ 9.11 Real-World Cloud Architecture for ML

```
                    ┌────────────────────────┐
                    │ Container Registry (ECR/AR) │
                    └───────────────┬───────────┘
                                    ▼
                          ┌────────────────────────┐
                          │ ML Deployment Target    │
                          │ (ECS / Cloud Run / App Service) │
                          └───────────────┬───────────┘
                                          ▼
                            ┌──────────────────────────┐
                            │    Load Balancer          │
                            └───────────────┬──────────┘
                                            ▼
                                 ┌────────────────────────┐
                                 │   Public API Endpoint   │
                                 └────────────────────────┘
```

---

# 🧪 9.12 Exercises

### **Exercise 1**
Deploy a Dockerized FastAPI ML service to EC2.

### **Exercise 2**
Deploy the same service to Cloud Run.

### **Exercise 3**
Add an Nginx reverse proxy to your EC2 deployment.

### **Exercise 4**
Set up auto-scaling on ECS or Cloud Run.

---

# 📝 9.13 Summary

- EC2 = simple VM hosting  
- ECS = scalable container orchestrator  
- Lambda = serverless ML  
- Cloud Run = easiest, cheapest managed platform  
- Azure App Service = container-based hosting  
- Use Nginx for reverse proxying  
- Load balancers ensure scalability  
- Auto-scaling ensures reliability  

Cloud deployment is a key MLOps capability — providing the foundation for reliable production ML.

---

# 🎉 End of Chapter 9
