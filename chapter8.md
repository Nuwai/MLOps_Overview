# 📘 Chapter 8 — Monitoring & Observability for ML Systems

Monitoring is one of the **most critical** components of MLOps.  
A model running in production will **degrade** over time due to:

- Data drift  
- Concept drift  
- Software changes  
- Infrastructure failures  
- Latency spikes  
- Increased load  

This chapter teaches how to design **monitoring, logging, metrics, drift detection, and observability** for production ML systems using **Prometheus, Grafana, Loki, CloudWatch**, and ML-specific tools.

---

# 🧠 8.1 Why Monitoring Is Essential

In ML, monitoring is harder than traditional software.

### Traditional software checks:
- CPU  
- Memory  
- Error rates  
- Availability  

### ML systems also require:
- Prediction quality  
- Input data distribution  
- Output distribution  
- Latency per inference  
- Model drift  
- Data drift  
- Feature importance drift  

If you are not monitoring these, your model may be **producing wrong results for months** without anyone noticing.

---

# 🧩 8.2 Monitoring vs Observability

| Concept | Meaning |
|--------|----------|
| **Monitoring** | Collecting and visualizing metrics |
| **Logging** | Recording events & errors |
| **Tracing** | Following requests end-to-end |
| **Observability** | Understanding system behavior through metrics, logs, traces |

Observability = the complete picture.

---

# 📡 8.3 What to Monitor in ML Systems

## 1️⃣ **Infrastructure Metrics**
- CPU usage  
- GPU memory  
- System memory  
- Disk usage  
- Network throughput  

## 2️⃣ **Application Metrics**
- Request count  
- Latency (p50, p90, p99)  
- Timeouts  
- Error codes  
- Queue length  

## 3️⃣ **Model-Specific Metrics**
- Prediction values  
- Confidence scores  
- Output distribution  
- Feature distribution  
- Missing values  
- Model size  
- Serving speed  

## 4️⃣ **Business Metrics**
Depends on the domain:

- Fraud catch rate  
- Recommendation CTR  
- Churn prediction accuracy  
- Revenue impact  

---

# 📊 8.4 Latency, Throughput, Error Rates

These three metrics represent the **Golden Signals of Monitoring** (Google SRE):

## Latency
Time to serve prediction.

Example:
- p50 = median  
- p90 = heavy load  
- p99 = worst-case  

## Throughput
Requests per second (RPS).

## Error Rate
Fraction of requests failing.

```
error_rate = failed_requests / total_requests
```

---

# ❗ 8.5 Model Drift (Critical ML Monitoring)

There are **three main types of drift**.

## 1️⃣ Data Drift
Input distribution changes.

Example:
- Age in training = 20–40  
- Age in production = 40–70  

## 2️⃣ Concept Drift
Relationship between features and target changes.

Example:
- Before COVID → customers buy work shirts  
- During COVID → customers buy pajamas  

## 3️⃣ Model Drift
Model gradually becomes less accurate.

---

# 📉 8.6 Drift Detection Tools

### 🟦 **EvidentlyAI**
- Open-source  
- Provides dashboards and reports  
- Tracks data drift, model drift, and data quality  

### 🟩 **WhyLabs**
- Cloud-based  
- ML observability platform  

### 🟧 **Arize AI**
- Model monitoring and explainability  

### 🟨 **SageMaker Model Monitor**
- Monitoring inside AWS  

---

# 🔍 8.7 Monitoring Architecture for ML

```
                ┌─────────────────┐
                │   ML API (App)  │
                └───────┬────────┘
                        ▼
            ┌─────────────────────┐
            │ Metrics Exporter     │
            │ (Prometheus Client)  │
            └─────────┬───────────┘
                      ▼
            ┌─────────────────────┐
            │     Prometheus       │
            └─────────┬───────────┘
                      ▼
            ┌─────────────────────┐
            │      Grafana         │
            └─────────────────────┘
```

---

# 📡 8.8 Prometheus for ML Metrics

Install:

```bash
pip install prometheus-client
```

Add metrics to FastAPI:

```python
from prometheus_client import Counter, Histogram

REQUEST_COUNT = Counter("api_requests", "Total API Requests")
LATENCY = Histogram("api_latency_seconds", "API Latency")

@app.post("/predict")
def predict(data: Input):
    REQUEST_COUNT.inc()

    with LATENCY.time():
        pred = model.predict(data.to_array())

    return {"prediction": float(pred[0])}
```

Prometheus scrapes metrics at:

```
/metrics
```

---

# 📊 8.9 Grafana Dashboards

Grafana visualizes:
- Latency percentiles  
- Traffic  
- Errors  
- Drift chart  
- Model performance  

You create:
- Panels  
- Alerts  
- Thresholds  

Common ML dashboards:
- Input feature histograms  
- Confidence scores  
- Data drift report  
- Real-time prediction distribution  

---

# 🧾 8.10 Logging with Loki (or CloudWatch)

FastAPI logging:

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("ml_api")

@app.get("/health")
def health():
    logger.info("Health check OK")
    return {"status": "ok"}
```

Send logs to:
- Loki (open-source)  
- CloudWatch (AWS)  
- Elasticsearch / OpenSearch  

---

# 🔐 8.11 Tracing with OpenTelemetry

Trace ML inference pipeline:

- request received  
- preprocessing  
- inference  
- postprocessing  
- response  

Add traces:

```bash
pip install opentelemetry-sdk opentelemetry-instrumentation-fastapi
```

---

# 🧪 8.12 Monitoring Model Quality (Online)

Offline evaluation only checks:
- Test accuracy  
- Validation metrics  

**But in production, labels are often delayed or unavailable.**

Solutions:
- Proxy metrics (confidence distribution)  
- Population stability index (PSI)  
- KL divergence  
- Data drift checks  

---

# 🛑 8.13 Alerting for ML Failures

Alerts trigger when:

- Latency > threshold  
- Error rate > threshold  
- Data drift detected  
- Missing values spike  
- Confidence < low threshold  
- Feature distribution shifts  

Example alert rule (Prometheus):

```yaml
alert: HighErrorRate
expr: rate(http_requests_total{status="500"}[5m]) > 0.1
for: 2m
```

---

# 🛠️ 8.14 Integrating Monitoring into CI/CD

CI/CD pipeline includes:

- API health test  
- Metrics endpoint test  
- Drift report test  
- Logging test  

Example:

```yaml
- name: Check /metrics
  run: curl http://localhost:8000/metrics
```

---

# 📦 8.15 Real-World ML Monitoring Stack

```
          ┌────────────────────────┐
          │      ML API Server      │
          └───────────┬────────────┘
                      ▼
        ┌────────────────────────────┐
        │      Prometheus Scraper     │
        └───────────┬────────────────┘
                    ▼
        ┌────────────────────────────┐
        │        Grafana Dashboard    │
        └───────────┬────────────────┘
                    ▼
        ┌────────────────────────────┐
        │      Drift Detector (Evidently) │
        └───────────┬────────────────┘
                    ▼
        ┌────────────────────────────┐
        │         Alerts System       │
        └────────────────────────────┘
```

---

# ❗ 8.16 Common ML Monitoring Mistakes

❌ Monitoring only CPU/GPU  
❌ Not tracking input distributions  
❌ No drift detection  
❌ No latency percentiles  
❌ No logs or traces  
❌ No business metrics  
❌ Not testing metrics endpoint in CI  

---

# 🧪 8.17 Exercises

### **Exercise 1**
Add Prometheus counters and histograms to your FastAPI app.

### **Exercise 2**
Create a Grafana dashboard with:
- latency p95  
- request count  
- error rate  

### **Exercise 3**
Generate a drift report using EvidentlyAI.

### **Exercise 4**
Write alert conditions for:
- latency spikes  
- drift detection  
- high error rate  

---

# 📝 8.18 Summary

- Monitoring is essential for production ML  
- Observe infrastructure, API, and ML-specific metrics  
- Detect drift early  
- Use Prometheus + Grafana for metrics & dashboards  
- Use Loki/CloudWatch for logs  
- Use Evidently/WhyLabs for drift  
- Build alerts for ML failures  

---

# 🎉 End of Chapter 8
