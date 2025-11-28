# 📘 Chapter 10 — Kubernetes for MLOps (Beginner-Friendly but Deep)

Kubernetes (K8s) is the **industry standard** for scaling, orchestrating, and deploying machine learning systems in production.

This chapter is designed to take you from **zero → production-ready Kubernetes ML engineer**, with:

- Pods, Deployments, Services  
- ConfigMaps & Secrets  
- Ingress  
- Autoscaling (HPA)  
- GPU workloads  
- ML inference workloads  
- Kubernetes YAML  
- Complete architecture diagrams  
- Best practices  
- Real-world examples used by top companies  

---

# 🔥 10.1 Why Kubernetes for ML?

ML workloads need:

- Auto-scaling  
- High availability  
- Load balancing  
- Rolling updates  
- Canary deployment  
- GPU scheduling  
- Reproducible environments  
- Infrastructure as code  
- Multi-model deployment  
- Multi-tenant environments  

Kubernetes provides all these out of the box.

### Real Companies Using Kubernetes for ML
- Uber (Michelangelo)  
- Netflix (Metaflow + Titus)  
- Airbnb  
- Google  
- Shopify  
- Spotify  

---

# 🧠 10.2 Core Kubernetes Concepts (ML Engineer Version)

Here are the essentials you must know.

## **1️⃣ Pod**
The smallest deployable unit.

```
Pod = 1 or more containers (usually 1 ML container)
```

## **2️⃣ Deployment**
Manages Pods, ensures:
- Desired number of replicas  
- Rolling updates  
- Rollbacks  

## **3️⃣ Service**
Gives Pods a stable **network endpoint**.

Types:
- ClusterIP  
- NodePort  
- LoadBalancer  

## **4️⃣ Ingress**
Provides:
- Public URL  
- HTTPS  
- Routing  
- Domain names  

## **5️⃣ ConfigMap**
Stores configuration that is NOT sensitive.

## **6️⃣ Secret**
Stores **sensitive** values:
- API keys  
- DB passwords  

## **7️⃣ Horizontal Pod Autoscaler (HPA)**
Scales Pods based on:
- CPU  
- Memory  
- Custom metrics (like latency)  

## **8️⃣ GPU Scheduling**
Nodes can have GPUs.  
Pods can request GPUs using resource limits.

---

# 🧱 10.3 Kubernetes Architecture (Diagram)

```
                      ┌───────────────────────────┐
                      │        Load Balancer       │
                      └─────────────┬─────────────┘
                                    ▼
                         ┌────────────────────┐
                         │      Ingress       │
                         └───────────┬────────┘
                                     ▼
             ┌──────────────────────────────────────────────┐
             │                 Services                      │
             └───────────┬──────────────────┬───────────────┘
                         ▼                  ▼
               ┌────────────────┐   ┌──────────────────┐
               │ Deployment A   │   │ Deployment B     │
               └─────────┬──────┘   └────────────┬────┘
                         ▼                        ▼
                 ┌────────────┐            ┌────────────┐
                 │   Pod 1    │            │   Pod 1    │
                 └────────────┘            └────────────┘
```

---

# 🚀 10.4 Deploying an ML API on Kubernetes

Let’s deploy a FastAPI inference service.

---

# 📄 10.5 Example Kubernetes YAML (Complete Deployment)

Create file: `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ml-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: ml-api
  template:
    metadata:
      labels:
        app: ml-api
    spec:
      containers:
      - name: ml-api
        image: myrepo/mlapi:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "200m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

---

# 🌐 10.6 Kubernetes Service (Expose Deployment)

Create file: `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ml-api-service
spec:
  type: LoadBalancer
  selector:
    app: ml-api
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

This exposes your ML API via a cloud provider load balancer.

---

# 🌍 10.7 Ingress (Domain + HTTPS)

Create file: `ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ml-api-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: mlapi.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: ml-api-service
            port:
              number: 80
```

---

# 🔐 10.8 ConfigMaps (Non-secret config)

Example: `configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ml-api-config
data:
  MODEL_PATH: "/models/model.pkl"
```

Mount it:

```yaml
env:
- name: MODEL_PATH
  valueFrom:
    configMapKeyRef:
      name: ml-api-config
      key: MODEL_PATH
```

---

# 🔐 10.9 Secrets (Sensitive values)

Example: `secret.yaml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: ml-api-secret
type: Opaque
data:
  API_KEY: dGVzdF9hcGlfa2V5  # base64 encoded
```

Use it:

```yaml
env:
- name: API_KEY
  valueFrom:
    secretKeyRef:
      name: ml-api-secret
      key: API_KEY
```

---

# 🤖 10.10 Adding GPU Support (Important for ML)

Add this to your Pod spec:

```yaml
resources:
  limits:
    nvidia.com/gpu: 1
```

Requires:
- GPU-enabled node  
- NVIDIA device plugin  

---

# 📈 10.11 Autoscaling with HPA (CPU-based)

Create: `hpa.yaml`

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: ml-api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ml-api
  minReplicas: 3
  maxReplicas: 15
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

Kubernetes increases Pod count when CPU > 70%.

---

# 🧪 10.12 Autoscaling with Custom ML Metrics

Use **Prometheus Adapter** for:

- Latency-based scaling  
- QPS-based scaling  
- Drift-based scaling  

Example:

```
requests_per_second > 200 → scale up
```

---

# 🏎️ 10.13 Canary Deployments (Safe ML Deployment)

Use two Deployments:
- Stable version  
- Candidate version  

Route traffic gradually:

```
90% traffic → v1
10% traffic → v2
```

Tools:
- Istio  
- Linkerd  
- NGINX ingress  

---

# 🛰️ 10.14 Rolling Updates (Built-in)

Update deployment:

```bash
kubectl apply -f deployment.yaml
```

K8s automatically:
- Spins up new Pods  
- Shuts down old Pods  
- Ensures zero downtime  

Rollback:

```bash
kubectl rollout undo deployment/ml-api
```

---

# 🐳 10.15 Local Development with Minikube or Kind

Minikube:

```bash
minikube start
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Kind (Kubernetes-in-Docker) is also good for CI testing.

---

# 🌎 10.16 Production Architecture for ML on Kubernetes

```
                       ┌──────────────────────────┐
                       │ Container Registry (ECR)  │
                       └──────────────┬───────────┘
                                      ▼
                           ┌────────────────────┐
                           │ Kubernetes Cluster  │
                           └──────────────┬─────┘
                                          ▼
                         ┌─────────────────────────────────┐
                         │   ML API Deployment + HPA       │
                         └────────────────┬────────────────┘
                                          ▼
                             ┌──────────────────────────┐
                             │       Service (LB)       │
                             └──────────────────────────┘
                                          ▼
                                External Public Traffic
```

---

# 🧱 10.17 Common Kubernetes Mistakes in ML

❌ Putting large models inside Docker image  
❌ Not requesting enough memory  
❌ Running GPU workloads on CPU nodes  
❌ No autoscaling  
❌ No liveness/readiness probes  
❌ No resource limits  
❌ Not using Secrets for API keys  
❌ Exposing the API using NodePort instead of Ingress  

---

# 🧪 10.18 Exercises

### **Exercise 1**
Deploy your FastAPI ML model using Deployment + Service.

### **Exercise 2**
Add ConfigMap + Secret and pass configs to your app.

### **Exercise 3**
Create an HPA that scales based on CPU.

### **Exercise 4**
Modify Deployment to request GPU.

### **Exercise 5**
Create an Ingress route for `/predict`.

---

# 📝 10.19 Summary

- Kubernetes is the backbone of scalable ML production systems  
- Pods run containers, Deployments manage Pods  
- Services expose your ML API  
- Ingress provides domain + HTTPS  
- ConfigMaps and Secrets provide configuration  
- HPA provides auto-scaling  
- GPU scheduling enables deep learning inference  
- Real companies rely on Kubernetes for ML workloads  

---

# 🎉 End of Chapter 10
