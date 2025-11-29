# 📘 Chapter 3 — REST & API Fundamentals for ML Engineers

## 🧠 3.1 Introduction

APIs are the **backbone of modern ML deployment**.

To deploy machine learning models in production, you must understand:

- What APIs are  
- How HTTP works  
- How REST is designed  
- JSON structure  
- Status codes  
- Best practices  

This chapter provides a **beginner-friendly but complete** understanding of REST fundamentals from an ML engineering perspective.

---

# 📍 3.2 What Is an API?

**API = Application Programming Interface**  
It allows two software systems to communicate.

### 🔹 Simple Analogy
An API is like a **restaurant waiter**:
- You (client) place an order → waiter sends it to kitchen  
- Kitchen (server) prepares food  
- Waiter returns the result  

### 🔹 In ML Context
- Client sends **input data**  
- ML API returns **model predictions**

### Examples
- Email validation API  
- Text sentiment API  
- Image classification API  
- Fraud detection service  

---

# 📍 3.3 What Is HTTP?

**HTTP = HyperText Transfer Protocol**  
It is the language used when browsers, apps, and servers communicate.

### 🔹 Key Concepts
- **Client** → sends a request  
- **Server** → sends a response  

### ASCII Diagram

```
Client (App / User)
        │
        │  HTTP Request
        ▼
    ML API Server
        │
        │  HTTP Response
        ▼
     Prediction Output
```

---

# 📍 3.4 HTTP Requests vs Responses

## 🔹 HTTP Request Includes:
- URL  
- Method (GET, POST, PUT, DELETE…)  
- Headers  
- Body (for POST/PUT)  

### Example Request

```
POST /predict
Content-Type: application/json

{
  "age": 30,
  "income": 50000
}
```

---

## 🔹 HTTP Response Includes:
- Status code  
- Headers  
- Body (JSON, text, image…)  

### Example Response

```
200 OK
{
  "prediction": "approved",
  "score": 0.87
}
```

---

# 📍 3.5 HTTP Methods

| Method | Meaning | ML Usage |
|--------|---------|----------|
| **GET** | Retrieve data | Health check, metadata |
| **POST** | Send data | ML predictions |
| **PUT** | Replace item | Update model config |
| **PATCH** | Partial update | Update certain fields |
| **DELETE** | Remove resource | Delete model version |

### Example (ML Prediction)
```
POST /predict
```
We use `POST` because we're sending **input data**, not retrieving static content.

---

# 📍 3.6 What Is JSON?

**JSON = JavaScript Object Notation**  
It is the most common format for web and ML API communication.

### Example JSON

```json
{
  "name": "Alice",
  "age": 25,
  "is_member": true
}
```

### Why JSON?
- Human readable  
- Lightweight  
- Language independent  
- Fast to parse  
- FastAPI automatically converts JSON → Python objects  

---

# 📍 3.7 What Is REST?

**REST = Representational State Transfer**  
A design style for creating scalable, well-structured APIs.

### 🔹 REST Principles
- Stateless  
- Client–server separation  
- Resource-based endpoints  
- Standard methods (GET, POST…)  
- Structured responses  

### RESTful Endpoint Examples

```
GET /users
GET /users/123
POST /users
PUT /users/123
DELETE /users/123
```

### ML API Examples

```
POST /predict
GET /health
GET /model/info
```

---

# 📍 3.8 REST Best Practices (Important for ML APIs)

### ❌ Bad
```
/getPrediction
```

### ✔️ Good
```
/predict
```

### Follow These Principles:
- Use **nouns** for URLs  
- Always return **JSON**  
- Always return **status codes**  
- Use **versioning** (`/v1/predict`)  
- Document APIs (FastAPI auto-generates Swagger UI)  
- Validate inputs (Pydantic models)  

---

# 📍 3.9 API Status Codes

| Code | Meaning |
|------|---------|
| **200** | Success |
| **201** | Resource created |
| **400** | Invalid request |
| **401** | Unauthorized |
| **403** | Forbidden |
| **404** | Not found |
| **409** | Conflict |
| **422** | Validation error |
| **500** | Server error |

### Example (Prediction Success)

```
200 OK
{
  "prediction": "spam"
}
```

### Example (Bad Input)

```
400 Bad Request
{
  "error": "age must be an integer"
}
```

---

# 📍 3.10 Example REST Flow for ML Prediction

## 1️⃣ Client Sends Data

```
POST /predict
{
  "review": "This product is great!"
}
```

## 2️⃣ Server Processes Input
- Load model  
- Preprocess input  
- Inference  
- Postprocess  

## 3️⃣ Server Returns Model Output

```
200 OK
{
  "sentiment": "positive",
  "score": 0.92
}
```

---

# 📍 3.11 Simple ML API Example

### Input

```json
{
  "x1": 3.2,
  "x2": 1.4
}
```

### Output

```json
{
  "prediction": 0.73
}
```

---

# 📍 3.12 Good vs Bad API Design (ML Perspective)

### ❌ Bad

```
POST /predictModelV3Test
{
  "json_data": "3,1,5"
}
```

**Problems**
- Terrible naming  
- No schema  
- Ambiguous data format  
- No versioning  
- Hard to validate  

---

### ✔️ Good

```
POST /v1/predict
Content-Type: application/json

{
  "sepal_length": 3.0,
  "sepal_width": 1.0,
  "petal_length": 5.0,
  "petal_width": 1.8
}
```

**Benefits**
- Clear schema  
- Proper versioning  
- Easy to validate  
- Easy to document  

---

# 📍 3.13 Practice Questions

1. What is the difference between GET and POST?  
2. Why is JSON widely used in ML APIs?  
3. Write a sample POST request for predicting house prices.  
4. What does “stateless” mean in REST?  
5. Name three useful status codes for ML APIs.  

---

# 📍 3.14 Exercises

### **Exercise 1 — Create a REST Endpoint Spec**
Write an endpoint definition for:

```
POST /v1/churn/predict
```

Include input fields, output format, and status codes.

---

### **Exercise 2 — Identify Bad API Design**

Given:

```
POST /getPrediction
{
  "x": "3.2"
}
```

List all the design issues.

---

### **Exercise 3 — JSON Schema for Fraud Detection**
Write a JSON request body for a fraud detection ML model.

---

# 📍 3.15 Summary

- APIs enable communication between ML models and applications  
- HTTP relies on requests & responses  
- POST is used for ML prediction  
- JSON is standard for data exchange  
- REST encourages scalable, structured API design  
- Understanding REST is essential before learning FastAPI  

---
# Useful terminology

## ✅ 1. Stateless Architecture (Most Modern APIs)
Definition

- A system is stateless when the server does not store any information about the user between requests.
- Every request is independent and must contain all necessary information.

Analogy

- Imagine ordering food at a restaurant where:
- The waiter doesn’t remember you.
- Every time, you must give your full order again.

In API terms

A stateless API works like this:
```Client → (request+data) → Server → Response → Server forgets

Example

A typical REST API:

```
GET /profile
Authorization: Bearer <token>


- The server reads the token → verifies → returns profile.
- The server does not remember you for the next request.

Pros

- Scales easily
- No session storage needed
- Easy load balancing (any server can handle any request)

Cons

- Client must send authentication on every request
---
## ✅ 2. Stateful Architecture (Old style servers, some DB systems)

Definition

A system is stateful when the server remembers previous interactions.

The server stores:

- Session data
- User data
- Past actions

Analogy

- Going to a hair salon where:
- The stylist remembers your past style
- You don’t need to re-explain everything every time

In API terms

A stateful server works like this:

```
Client → Login → Server stores session →  
Client → next requests → Server uses stored session
```

Example

- PHP apps with server-side sessions:

```session_id=ABC123
```

Server stores:

- Logged-in user
- Cart items
- Preferences

Pros

- Convenient (no need to send full info every time)

Cons

- Harder to scale (session must stay on same server)
---
## ✅ 3. Serverless (e.g., AWS Lambda, Cloud Functions)
Definition

Serverless means:

- You do not manage servers
- Your code runs on demand
- It scales automatically
- You pay only when it runs
- Misunderstanding warning

❌ “Serverless” does NOT mean “no servers”…
✔ It means servers are hidden from you.

Analogy

Like taking a taxi:

- You don't own the car (server)
- You just call when you need it
- Pay only for the distance used

Characteristics

- Each function execution is short-lived
- Often stateless
- You cannot store memory in RAM between requests

Example

FastAPI running on AWS Lambda:

```POST /predict → triggers a function → returns result → function dies

Pros

- Zero maintenance
- Auto-scaling
- Cheap for small workloads

Cons

- Cold start delays
- Not good for long-running tasks
- Hard to store complex state

Requires sticky sessions or session replication

## 📌 Where They Are Used in Real Life

### Stateless

- FastAPI
- Django REST
- Flask APIs
- AWS API Gateway

### Stateful

- Old PHP apps using $_SESSION
- WebSockets (connection must be tracked)
- Games maintaining session data

### Serverless

- AWS Lambda
- Google Cloud Functions
- Netlify Functions
- Vercel serverless API routes

---
# 🎉 End of Chapter 3
