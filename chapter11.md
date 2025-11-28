# 📘 Chapter 11 — ML Pipelines & Orchestration (Airflow, Prefect, Kubeflow)

In modern MLOps, building a model is only **one small part** of the job.  
Data changes, models degrade, and systems must run on **automation**, not manual re-running.

This is where **ML pipelines** and **workflow orchestration** come in.

This chapter covers:

- What ML pipelines are  
- Batch vs real-time pipelines  
- Airflow  
- Prefect  
- Kubeflow  
- Feature stores  
- A full end-to-end training → evaluation → deployment pipeline  
- Best practices  
- Architecture diagrams  
- Real examples used by companies  

---

# 🧠 11.1 What Are ML Pipelines?

An ML pipeline is a **series of automated steps** that transform raw data → trained model → deployed system.

Example pipeline:

```
Data Ingestion → Data Validation → Feature Engineering →
Training → Evaluation → Model Registry → Deployment
```

Pipelines prevent:
- Manual work  
- Inconsistent results  
- Human errors  
- Non-reproducible workflows  

---

# ⏱️ 11.2 Batch vs Real-Time Pipelines

## **Batch Pipelines**
Run on a schedule:
- hourly  
- daily  
- weekly  

Examples:
- Daily retrain  
- Batch prediction  
- ETL jobs  

Tools:
- Airflow  
- Prefect  
- Kubeflow  
- AWS Batch  
- GCP Composer  

---

## **Real-time Pipelines**
Triggered by events (streaming).

Examples:
- Fraud detection  
- Recommendation ranking  
- Real-time monitoring  
- Alerts  

Tools:
- Kafka  
- Flink  
- Spark Streaming  
- Kinesis  

---

# 📐 11.3 Pipeline Example Diagram

```
          ┌─────────────────────┐
          │     Raw Data        │
          └─────────┬───────────┘
                    ▼
         ┌───────────────────────┐
         │ Data Preprocessing    │
         └─────────┬────────────┘
                    ▼
     ┌─────────────────────────────┐
     │   Feature Engineering       │
     └───────────┬────────────────┘
                 ▼
       ┌────────────────────────┐
       │      Training          │
       └──────────┬────────────┘
                  ▼
    ┌──────────────────────────┐
    │     Evaluation           │
    └───────────┬─────────────┘
                ▼
   ┌───────────────────────────┐
   │     Model Registry        │
   └───────────┬──────────────┘
               ▼
  ┌────────────────────────────┐
  │        Deployment          │
  └────────────────────────────┘
```

---

# 🚀 11.4 Tools for ML Orchestration

## **1️⃣ Airflow**
- Most widely-used workflow orchestrator  
- DAG-based (Directed Acyclic Graphs)  
- Great for data + ML  
- Scheduling, retry logic, logging, parallelism  

## **2️⃣ Prefect**
- Pythonic, developer-friendly  
- Easier than Airflow  
- Cloud UI available  
- Async + dynamic pipelines  

## **3️⃣ Kubeflow**
- Kubernetes-native ML pipelines  
- Great for:
  - GPU workloads  
  - Large-scale deep learning  
  - AutoML  
  - Advanced ML workflows  

---

# 🛠️ 11.5 Airflow Deep Dive

Airflow defines workflows as **DAGs (Directed Acyclic Graphs)**.

### Install:

```bash
pip install apache-airflow
```

### Example Airflow DAG

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def train():
    print("Training model...")

def evaluate():
    print("Evaluating...")

with DAG(
    "ml_pipeline",
    start_date=datetime(2023, 1, 1),
    schedule_interval="@daily",
) as dag:

    train_task = PythonOperator(
        task_id="train",
        python_callable=train
    )

    eval_task = PythonOperator(
        task_id="evaluate",
        python_callable=evaluate
    )

    train_task >> eval_task
```

---

# ⚙️ 11.6 Prefect Deep Dive

Prefect uses **Flows** and **Tasks**.

Install:

```bash
pip install prefect
```

### Example Prefect Flow

```python
from prefect import task, flow

@task
def extract():
    return [1,2,3]

@task
def train(data):
    return sum(data)

@flow
def pipeline():
    data = extract()
    result = train(data)
    print(result)

pipeline()
```

### Why ML engineers love Prefect:
- Easier debugging  
- Works locally + cloud  
- More flexible than Airflow  

---

# 🤖 11.7 Kubeflow Pipelines

Kubeflow = Kubernetes-native ML platform.

Supports:
- Scalable training  
- Distributed training  
- GPU/TPU  
- Feature stores  
- Full ML lifecycle  

### Kubeflow pipeline example:

```python
def train_op():
    return dsl.ContainerOp(
        name="train model",
        image="ml-train:latest",
        command=["python", "train.py"]
    )

@dsl.pipeline(name="ml-pipeline")
def pipeline():
    train = train_op()
```

Deploy with:

```bash
kubectl apply -f pipeline.yaml
```

---

# 🧱 11.8 ML Pipelines vs General Workflow Pipelines

| Task | Airflow | Kubeflow |
|------|--------|----------|
| ETL | ✅ | 🔶 |
| ML | Good | Excellent |
| GPU training | ❌ | ✅ |
| Kubernetes native | ❌ | ✅ |
| Enterprise support | High | Medium |
| Simple setup | Medium | Hard |
| Workflow flexibility | High | Medium |

---

# 🧬 11.9 Feature Stores

Feature Store = consistent, validated feature management.

Examples:
- Feast  
- Tecton  
- Hopworks  
- Vertex AI Feature Store  
- SageMaker Feature Store  

### Why use feature stores?

Without them → training uses different features than production.

### With feature store:

```
               ┌───────────────┐
               │ Feature Store  │
               └───────┬───────┘
                       ▼
            ┌─────────────────────────┐
            │ Same features for:      │
            │ - Training              │
            │ - Online inference      │
            └─────────────────────────┘
```

---

# 🔄 11.10 Example End-to-End Pipeline (Full ML Workflow)

This is how real companies automate ML retraining.

### Step 1 — Data Ingestion

```python
def ingest():
    df = read_from_s3("dataset.csv")
    return df
```

### Step 2 — Validation

```python
validate(df)
```

### Step 3 — Feature Engineering

```python
X, y = features(df)
```

### Step 4 — Training

```python
model.fit(X, y)
```

### Step 5 — Evaluation

```python
metrics = evaluate(model)
```

### Step 6 — Register Model (MLflow)

```python
mlflow.log_model(model, "model")
mlflow.register_model(...)
```

### Step 7 — Deployment

- Push Docker image  
- CI/CD triggers  
- Kubernetes rolling update  

---

# 🔗 11.11 CI/CD + Pipelines Integration

A hybrid architecture:

```
Git push → GitHub Actions → Build model/Docker → Registry
                               ↓
                         Airflow scheduler → Daily pipeline → Model Registry
                               ↓
                           Deployment → K8s
```

---

# 🛰️ 11.12 Reproducibility With Pipelines

Good pipelines ensure:

- Same environment for training  
- Same data version  
- Same hyperparameters  
- Code version tracking  
- Automatic metadata logging  

Tools:
- MLflow  
- DVC  
- Kubeflow metadata server  

---

# 📈 11.13 Monitoring Pipelines

Track:

- Task success/failure  
- Task runtime  
- Upstream/downstream dependency failures  
- Retries  
- Alerts  

Tools:
- Airflow UI  
- Prefect Cloud  
- Kubeflow dashboard  

---

# 🧱 11.14 Real-World ML Pipeline Architecture Diagram

```
                    ┌──────────────────────────┐
                    │      Data Warehouse       │
                    └───────────────┬──────────┘
                                    ▼
                             Data Ingestion
                                    ▼
                         Data Validation (GX)
                                    ▼
                         Feature Engineering (Feast)
                                    ▼
                         Training Jobs (K8s/Batch)
                                    ▼
                          Evaluation (CI/CD gates)
                                    ▼
                      Model Registry (MLflow/DVC)
                                    ▼
                    ┌───────────────┴──────────────┐
                    ▼                              ▼
              Batch Deployment              Real-Time Deployment
                    │                              │
            Batch Inference Jobs            FastAPI / gRPC API
```

---

# ❗ 11.15 Common Pitfalls in ML Pipelines

❌ Manual retraining  
❌ Not versioning datasets  
❌ Pipelines with hidden states  
❌ Mixing testing + training environments  
❌ No monitoring for pipeline failures  
❌ No retry or backoff system  
❌ No lineage tracking  

---

# 🧪 11.16 Exercises

### **Exercise 1 — Airflow DAG**
Create a DAG with:
- Ingestion  
- Training  
- Evaluation  

### **Exercise 2 — Prefect**
Convert your local training script into a Prefect flow.

### **Exercise 3 — Kubeflow**
Containerize your training job and run it in a Kubeflow pipeline.

### **Exercise 4 — Feature Store**
Define two features for a dataset (one offline, one online).

---

# 📝 11.17 Summary

- ML pipelines automate the ML lifecycle  
- Airflow → general workflow orchestration  
- Prefect → Pythonic and modern  
- Kubeflow → ML-focused & Kubernetes native  
- Feature stores ensure consistent features  
- Full pipelines handle ingestion → validation → training → registry → deployment  
- Pipelines reduce human error and enable scalability  

---

# 🎉 End of Chapter 11
