# 📘 Chapter 2 — ML Lifecycle Core Concepts

## 🧠 2.1 Introduction

The Machine Learning lifecycle is the **end-to-end process** that transforms raw data into production-ready ML systems.  
In MLOps, every stage of this lifecycle is automated, reproducible, observable, and scalable.

This chapter covers:

- Data collection  
- Data validation & data quality  
- Feature engineering  
- Model training  
- Evaluation  
- Drift  
- Retraining  
- Full ML system architecture  

---

# 📍 2.2 Data Collection

Data collection is the **foundation** of ML.

### 🔹 Common Data Sources
- Databases (SQL, NoSQL)  
- Data warehouses (Snowflake, BigQuery)  
- Data lakes (S3, GCS)  
- REST APIs  
- Event streams (Kafka)  
- Application logs  
- IoT sensor data  
- User inputs  

### 🔹 Requirements in MLOps
- **Automated ingestion** (Airflow, Prefect, Kafka)  
- **Versioned datasets** (DVC, Delta Lake)  
- **Data validation** to prevent corrupted data  

### 📦 Data Flow (Diagram)

```
Raw Data → Ingestion → Storage (Lake/Warehouse)
```

---

# 📍 2.3 Data Quality

Data quality determines model accuracy and reliability.

### 🔹 Key Dimensions

| Dimension | Meaning |
|----------|---------|
| Completeness | Missing values? |
| Accuracy | Are values correct? |
| Consistency | Schema alignment |
| Freshness | Up-to-date data |
| Uniqueness | Duplicates? |
| Validity | Expected ranges |

### 🔹 Common Issues
- Missing fields  
- Wrong labels  
- Corrupted values  
- Outliers  
- Different units (e.g., meters vs feet)  

### 🔹 Tools
- Great Expectations  
- TensorFlow Data Validation  
- Evidently AI  

---

# 📍 2.4 Feature Engineering

Feature engineering transforms raw data into ML-ready features.

### 🔹 Common Techniques
- Scaling & normalization  
- One-hot encoding  
- Embeddings  
- Aggregations (rolling averages)  
- Polynomial features  
- Feature selection (e.g., removing noisy inputs)  

### 🔹 Feature Stores (Important!)
- Ensure training & production use **the same features**  
- Offline + online consistency  
- Examples: Feast, Tecton, Vertex AI Feature Store  

---

# 📍 2.5 Model Training

Training is where models learn patterns.

### 🔹 Training Steps
1. Train/validation/test split  
2. Model/algorithm selection  
3. Hyperparameter tuning  
4. Experiment tracking  
5. Model artifact generation  

### 🔹 MLOps Requirements
- Reproducible environment (Docker)  
- Clear separation between config & code  
- Automated runs (Airflow/Prefect/Kubeflow)  
- Tracking (MLflow, Weights & Biases)  

---

# 📍 2.6 Model Evaluation

Your model must be measured properly.

### 🔹 Classification Metrics
- Accuracy  
- Precision  
- Recall  
- F1-score  
- ROC-AUC  

### 🔹 Regression Metrics
- RMSE  
- MAE  
- R²  

### 🔹 Production Metrics (Critical)
- Latency  
- Throughput (RPS)  
- Failure rate  
- Confidence distribution  
- Drift indicators  

### 📦 Evaluation Flow

```
Model Output → Compare Predictions → Metrics → Decision
```

---

# 📍 2.7 Drift (Data Drift, Concept Drift, Model Drift)

Drift causes model degradation.

### 🔹 1. Data Drift  
Input distribution changes over time.

Example: ages in real data shift from (20–40) → (40–70).

### 🔹 2. Concept Drift  
The relationship between X and y changes.

Example: buying habits change due to external events (COVID, economic factors).

### 🔹 3. Model Drift  
Model gradually loses accuracy due to:
- Data drift  
- Concept drift  
- Changes in environment  

### 🔹 Drift Detection Tools
- EvidentlyAI  
- WhyLabs  
- Arize AI  

---

# 📍 2.8 Retraining

Retraining ensures the model stays relevant.

### 🔹 Retraining Triggers
- Drift detected  
- Scheduled retraining  
- New labeled data available  
- Model performance degrade  

### 🔹 Retraining Steps
1. Re-ingest data  
2. Validate data  
3. Recompute features  
4. Retrain model  
5. Evaluate and compare  
6. Register new version  
7. Deploy to production  

### 📦 Retraining Pipeline

```
New Data → Validation → Feature Engineering → Training → Evaluation → Model Registry → Deployment
```

---

# 📍 2.9 ML System Architecture (Production)

A complete ML system includes:

### 🔹 Components

#### **Data Layer**
- ETL pipelines  
- Data quality monitoring  
- Feature store  

#### **Training Layer**
- Training pipelines  
- Experiment tracking  
- Model registry  

#### **Serving Layer**
- FastAPI  
- Docker  
- Kubernetes  

#### **Monitoring Layer**
- Drift detection  
- Latency tracking  
- Predictive metrics  

### 📦 Architecture Diagram

```
Data → ETL → Feature Store → Training → Evaluation → Registry → Deployment → Monitoring → Retraining
```

---

# 📍 2.10 Summary Table

| Concept | Summary |
|--------|---------|
| Data Collection | Automate & validate raw data |
| Data Quality | Critical for model reliability |
| Feature Engineering | Transform raw → ML-ready |
| Training | Automated + reproducible |
| Evaluation | Use ML + production metrics |
| Drift | Detect shifts in data/model |
| Retraining | Continuous improvement |
| Architecture | Full ML lifecycle management |

---

# 📍 2.11 Quiz

### **Q1:** What is data drift?  
A) Code changes  
B) Input distribution changes  
C) GPU temperature  
➡️ **Answer: B**

### **Q2:** Which is a regression metric?  
A) F1  
B) ROC-AUC  
C) RMSE  
➡️ **Answer: C**

### **Q3:** What tool is used to detect drift?  
➡️ **EvidentlyAI**

---

# 📍 2.12 Exercises

### **Exercise 1 — Data Quality Checklist**  
List 5 data quality issues that could affect your model.

### **Exercise 2 — Drift Example**  
Describe a real-life case of concept drift.

### **Exercise 3 — Feature Engineering**  
Pick a dataset and propose 3 engineered features.

### **Exercise 4 — Evaluation Metrics**  
For a classification task, decide which metric is most important and why.

---

# 📍 2.13 Lab Activity

Write a mini ML lifecycle pipeline description:

```
1. Collect raw data
2. Validate data quality
3. Produce engineered features
4. Train model
5. Evaluate using metrics
6. Register model version
7. Deploy to production
```

---

# 🎉 End of Chapter 2
