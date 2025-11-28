# 📘 Chapter 7 — Model Registry & Versioning (MLflow, DVC, Experiments)

Model versioning is one of the **core pillars of MLOps**.  
Modern ML systems must track:

- Which **data** trained a model  
- Which **code** built it  
- Which **parameters** were used  
- Which **metrics** it achieved  
- Which **model artifact** was deployed  
- Which **version** is currently running in production  

This chapter explains how real companies track and manage models using **MLflow**, **DVC**, and **model registries**.

---

# 🧠 7.1 Why Model Versioning Matters

Machine learning is different from traditional software:

- Code changes → different results  
- Data changes → different results  
- Hyperparameters → different results  
- Random seeds → different results  

Without proper versioning, ML becomes **non-reproducible, unpredictable, and risky**.

### Real problems:
- “Which model is running in production?” → No answer  
- “Why did accuracy drop?” → No data version tracking  
- “Can we reproduce the last good model?” → Impossible  

Model versioning solves all of these.

---

# 🗂️ 7.2 What Is a Model Registry?

A **Model Registry** is a centralized system for:

- Registering models  
- Storing versioned model artifacts  
- Adding metadata (author, metrics, description)  
- Approving/rejecting models  
- Tracking production/staging/dev versions  
- Managing lifecycle events  

### Model lifecycle phases:

```
Saved → Registered → Staging → Production → Archived
```

---

# 🧪 7.3 MLflow Overview

MLflow is the most widely used open-source tool for:

- Experiment tracking  
- Model versioning  
- Model registry  
- Reproducible ML pipelines  

Install:

```bash
pip install mlflow
```

Start UI:

```bash
mlflow ui
```

Open in browser:

```
http://127.0.0.1:5000
```

---

# 🏷️ 7.4 Experiment Tracking with MLflow

Example training script:

```python
import mlflow
import mlflow.sklearn
from sklearn.ensemble import RandomForestClassifier

mlflow.start_run():

    model = RandomForestClassifier(n_estimators=100)
    model.fit(X_train, y_train)

    acc = model.score(X_test, y_test)

    mlflow.log_param("n_estimators", 100)
    mlflow.log_metric("accuracy", acc)
    mlflow.sklearn.log_model(model, "model")

mlflow.end_run()
```

### MLflow automatically tracks:
- Parameters  
- Metrics  
- Artifacts (models, plots, files)  
- Git commit hash  
- Timestamp  

---

# 🎯 7.5 Model Registry with MLflow

Register a model:

```python
result = mlflow.register_model(
    "runs:/<run_id>/model",
    "churn_model"
)
```

Transition model to production:

```python
from mlflow.tracking import MlflowClient

client = MlflowClient()
client.transition_model_version_stage(
    name="churn_model",
    version=1,
    stage="Production"
)
```

---

# 📦 7.6 Loading Models from MLflow Registry

```python
import mlflow.pyfunc

model = mlflow.pyfunc.load_model("models:/churn_model/Production")
pred = model.predict({"age": 30, "income": 40000})
```

This allows **production services** (FastAPI, Lambda, Batch jobs) to load the latest model.

---

# 📁 7.7 DVC (Data Version Control)

DVC solves one critical problem:

> **Git cannot store large datasets or model artifacts.**

DVC provides:
- Data versioning  
- Model versioning  
- Pipelines  
- Storage (S3/GCS/local)  

Install:

```bash
pip install dvc
```

Initialize:

```bash
dvc init
```

---

# 🧪 7.8 Versioning Data with DVC

Track a dataset:

```bash
dvc add data/train.csv
```

This creates:
- `train.csv.dvc` file  
- Dataset stored in remote cache  

Add remote storage:

```bash
dvc remote add -d myremote s3://mybucket/data
```

Push:

```bash
dvc push
```

Anyone on your team can now:

```bash
git pull
dvc pull
```

And they will have **exactly the same dataset**.

---

# 🧱 7.9 DVC Pipelines

Example pipeline definition:

```bash
dvc run -n train \
  -d data/train.csv \
  -d train.py \
  -o model.pkl \
  python train.py
```

Run entire pipeline:

```bash
dvc repro
```

---

# 🆚 7.10 MLflow vs DVC — Which to Use?

| Feature | MLflow | DVC |
|--------|--------|------|
| Experiment tracking | ✅ | ❌ |
| Model registry | ✅ | ❌ |
| Data versioning | ❌ | ✅ |
| Pipeline orchestration | ❌ | ✅ |
| Cloud storage | ✓ | ✓ |
| Reproducibility | ✓ | ✓ |

**Best practice in real companies:**

> Use MLflow for model tracking + registry,  
> Use DVC for dataset versioning.

---

# 🔢 7.11 Versioning Strategies for ML

## 1️⃣ Model Versioning
Example:

```
churn_model:v1
churn_model:v2
```

## 2️⃣ Semantic Versioning

```
v1.0.0
v1.1.0
v2.0.0
```

## 3️⃣ Git Hash Versioning

```
model_4f8d11a.pkl
```

## 4️⃣ Dataset Versioning (DVC)

```
data@v4
data@v5
```

---

# 🗄️ 7.12 What to Store Along with a Model?

A model alone is **not enough**.  
You must store:

- Model artifact (pickle, ONNX, TorchScript)  
- Model signature  
- Parameters  
- Metrics  
- Environment (conda, pip freeze, Dockerfile)  
- Code version  
- Dataset version  
- Training timestamp  
- Author / owner  

This enables **total reproducibility**.

---

# 🧪 7.13 Example: Complete MLflow Training & Registry Script

```python
import mlflow
import mlflow.sklearn
from sklearn.linear_model import LogisticRegression

mlflow.set_experiment("churn")

with mlflow.start_run():

    model = LogisticRegression()
    model.fit(X_train, y_train)

    acc = model.score(X_test, y_test)

    mlflow.log_metric("accuracy", acc)
    mlflow.sklearn.log_model(model, artifact_path="model")

    model_info = mlflow.register_model(
        "runs:/{run_id}/model",
        "churn_model"
    )
```

---

# 🧰 7.14 Real-World Architecture for Model Registry

```
          ┌──────────────────┐
          │   Training Jobs   │
          └─────────┬─────────┘
                    ▼
            Experiment Tracking
                    ▼
            Model Registry (MLflow)
                    ▼
      ┌─────────────┴─────────────┐
      ▼                           ▼
Staging Environment         Production API
      │                           │
      └───────────► Monitoring ◄──┘
```

---

# 🚨 7.15 Common Mistakes

❌ Storing models only on local machines  
❌ No versioning for datasets  
❌ No tracking for hyperparameters  
❌ Manually naming model files  
❌ Deploying models without registry  
❌ No record of which model version is in production  

---

# 🧪 7.16 Exercises

### **Exercise 1**
Use MLflow to track:
- Two parameters  
- One metric  
- One model

### **Exercise 2**
Register a model and promote it to staging.

### **Exercise 3**
Initialize DVC and track `data.csv`.

### **Exercise 4**
Explain the difference between MLflow and DVC.

---

# 📝 7.17 Summary

- Model versioning is essential for reproducibility  
- MLflow handles experiment tracking & registry  
- DVC handles dataset + pipeline versioning  
- A good model registry stores metrics, artifacts, and metadata  
- Versioning strategies ensure traceability  

---

# 🎉 End of Chapter 7
