# 📘 Chapter 12 — Final Projects (3 Real-World MLOps Projects)

Congratulations on reaching the final chapter of the course.  
You now have the foundation and advanced skills across the entire MLOps lifecycle:

- ML modeling  
- FastAPI deployment  
- Docker  
- CI/CD  
- Model registry  
- Monitoring  
- Cloud deployment  
- Kubernetes orchestration  
- Pipeline automation  

To solidify your knowledge, this chapter gives you **three full, real-world MLOps projects** that will push you to apply everything you’ve learned.

---  

# 🎯 Project 1 — End-to-End REST ML API using FastAPI + Docker + Cloud Deployment

This project simulates deploying a **real ML model** as a production API.

## ✅ Objective
Build, containerize, and deploy a machine learning inference service using:

- FastAPI  
- Docker  
- CI/CD (GitHub Actions)  
- Cloud (AWS/GCP/Azure)  
- Monitoring endpoint `/metrics`  

---

## 📦 Step-by-Step Requirements

### **1️⃣ Train a Model**
Choose a dataset (Iris, Titanic, House Prices…).

Example:

```python
model.fit(X_train, y_train)
joblib.dump(model, "model.pkl")
```

### **2️⃣ Build FastAPI Inference Service**
Create `main.py`:

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib
import numpy as np

model = joblib.load("model.pkl")

app = FastAPI()

class Input(BaseModel):
    sepal_length: float
    sepal_width: float
    petal_length: float
    petal_width: float

@app.post("/predict")
def predict(inp: Input):
    X = np.array([[inp.sepal_length, inp.sepal_width, inp.petal_length, inp.petal_width]])
    pred = model.predict(X)[0]
    return {"prediction": int(pred)}
```

### **3️⃣ Add Prometheus Metrics**
Expose `/metrics` endpoint.

### **4️⃣ Create Dockerfile**
Use best practices.

### **5️⃣ Build & Run Docker**
```bash
docker build -t mlapi .
docker run -p 8000:80 mlapi
```

### **6️⃣ Add CI Pipeline**
Workflow should:

- Install dependencies  
- Run tests  
- Build Docker image  
- Push to registry  

### Example YAML snippet:

```yaml
- name: Build Docker
  run: docker build -t ${{ github.repository }}:latest .
```

### **7️⃣ Deploy to Cloud**

Choose one:

- **AWS EC2**
- **AWS ECS**
- **Google Cloud Run (recommended)**
- **Azure App Service**

### **8️⃣ Create README**
Include:

- API docs  
- Example input/output  
- Deployment instructions  

---

# 🎉 Deliverables for Project 1

- `main.py`  
- `model.pkl`  
- `Dockerfile`  
- `.github/workflows/ci.yml`  
- Cloud deployment URL  
- README.md  

---

# 🎯 Project 2 — Automated Retraining Pipeline (Data Drift → Auto Retrain → Auto Deploy)

This project simulates **real enterprise MLOps** using:

- Drift detection  
- Automatic retraining  
- Automatic model versioning  
- Automatic deployment  

---

## 🧠 Objective
Build a system that:

1. Detects data drift  
2. Retrains the model  
3. Evaluates it  
4. Registers new version  
5. Deploys automatically if it passes thresholds  

This pipeline should run on a **schedule** (daily/weekly).

---

## 📦 Components

### **1️⃣ Drift Detection (EvidentlyAI)**

Example script:

```python
from evidently.report import Report
from evidently.metrics import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=train_df, current_data=new_df)
result = report.as_dict()
```

Detect drift:

```python
if result["metrics"][0]["result"]["dataset_drift"]:
    trigger_retrain()
```

---

### **2️⃣ Training Pipeline**

Automate:

- Data loading  
- Validation  
- Feature engineering  
- Training  
- Evaluation  
- Model registry  

Use:

- Airflow  
- Prefect  
- Or a CRON-triggered Python script  

---

### **3️⃣ Model Registry Integration**

Use MLflow:

```python
mlflow.sklearn.log_model(model, "model")
```

Register:

```python
mlflow.register_model(..., "churn-model")
```

Promote automatically:

```python
if accuracy > 0.90:
    client.transition_model_version_stage(..., stage="Production")
```

---

### **4️⃣ Auto-Deployment**

Automate container build + rollout:

```bash
docker build -t myrepo/mlapi:v${VERSION} .
docker push myrepo/mlapi:v${VERSION}
kubectl rollout restart deployment/ml-api
```

or trigger Cloud Run deployment:

```bash
gcloud run deploy mlapi --image myrepo/mlapi:v${VERSION}
```

---

# ⚙️ Architecture Diagram for Project 2

```
     ┌────────────────────────┐
     │  Production Data Flow   │
     └──────────┬─────────────┘
                ▼
       ┌────────────────────┐
       │ Drift Detection     │
       └──────────┬─────────┘
                  ▼
        ┌───────────────────────┐
        │ Auto Retraining Job   │
        └──────────┬────────────┘
                   ▼
      ┌────────────────────────────┐
      │  Model Registry (MLflow)   │
      └──────────┬─────────────────┘
                 ▼
      ┌────────────────────────────┐
      │ Auto Deployment (CI/CD)    │
      └────────────────────────────┘
```

---

# 🎉 Deliverables for Project 2

- Drift detection script  
- Training pipeline (Airflow/Prefect/Kubeflow)  
- MLflow integration  
- CI/CD auto-deployment workflow  
- Documentation of thresholds and logic  

---

# 🎯 Project 3 — Full Kubernetes ML Architecture (Scalable Inference + Monitoring)

This project focuses on **production-scale deployment** using Kubernetes (K8s).

---

## 🧠 Objective

Design and deploy a **fully scalable ML inference platform** using:

- Kubernetes  
- Deployments  
- Services  
- Horizontal Pod Autoscaler  
- ConfigMaps & Secrets  
- Metrics (Prometheus)  
- Logging (Loki)  
- CI/CD  

---

# 📦 Required Components

### **1️⃣ Containerized ML API**
Same as Project 1.

### **2️⃣ Kubernetes Deployment**

Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ml-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mlapi
  template:
    metadata:
      labels:
        app: mlapi
    spec:
      containers:
      - name: mlapi
        image: myrepo/mlapi:latest
        ports:
        - containerPort: 80
```

---

### **3️⃣ Kubernetes Service**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mlapi-service
spec:
  type: LoadBalancer
  selector:
    app: mlapi
  ports:
  - port: 80
    targetPort: 80
```

---

### **4️⃣ Ingress + Domain**

Provide an HTTPS endpoint for public users.

---

### **5️⃣ Autoscaling (HPA)**

Scale based on CPU or custom Prometheus metrics:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: mlapi-hpa
spec:
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 80
```

---

### **6️⃣ Logging + Monitoring**

- Prometheus  
- Grafana  
- Loki  

Expose `/metrics` endpoint.

---

### **7️⃣ CI/CD Deployment Pipeline**

Every push → update Kubernetes cluster.

Example:

```yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Or a Helm chart.

---

# 📊 Architecture Diagram for Project 3

```
                      ┌──────────────────────────────┐
                      │      GitHub Actions CI/CD     │
                      └───────────────┬──────────────┘
                                      ▼
                         ┌────────────────────────┐
                         │  Container Registry     │
                         └───────────────┬─────────┘
                                         ▼
                           ┌────────────────────────┐
                           │   Kubernetes Cluster    │
                           └───────────────┬────────┘
                                           ▼
               ┌──────────────────────────────────────────┐
               │ Deployment + Service + Autoscaling (HPA) │
               └───────────────────────┬──────────────────┘
                                       ▼
                              ┌──────────────────────┐
                              │   Ingress + TLS      │
                              └──────────────────────┘
                                       ▼
                                Public ML API Users
```

---

# 🎉 Deliverables for Project 3

- Kubernetes YAML (deployment, service, ingress, HPA)  
- Dockerized ML API  
- Monitoring setup  
- CI/CD deployment pipeline  
- Architecture diagram  
- Final README.md  

---

# 🏁 Final Note

You now have:

- 3 real-world ML projects  
- Covering ML, APIs, Docker, CI/CD, Cloud, Kubernetes, Drift, and Pipelines  
- Full architecture diagrams  
- Production-level workflows  

These are the exact types of projects used in:

- FAANG interviews  
- Senior ML engineering roles  
- MLOps engineering portfolios  
- Bootcamps  
- Production systems  

---

# 🎓 Congratulations!
You've completed the **full 12-chapter MLOps + FastAPI + Deployment course**.

Add these projects to your GitHub portfolio and continue evolving your skills.

---

# 🏁 End of Chapter 12
