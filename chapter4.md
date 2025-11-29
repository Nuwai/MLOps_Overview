# 📘 Chapter 4 — FastAPI for ML Deployment (Deep & Complete)

FastAPI is one of the most popular frameworks for deploying machine learning models.
It is **fast**, **async-friendly**, **easy to use**, and built with **data validation** as a first-class citizen — perfect for ML.

This chapter covers:

- ASGI vs WSGI  
- FastAPI fundamentals  
- Endpoints, path/query params  
- Pydantic models  
- Loading ML models efficiently  
- File handling  
- Async vs sync  
- Middleware  
- Authentication  
- Logging, config, env vars  
- Real ML API examples (NLP + CV)  
- Good vs bad API design  

---

# 🧩 4.1 ASGI vs WSGI

## **WSGI (Old Standard)**
- Synchronous  
- Used by Flask, Django (without async)  
- Cannot efficiently handle long requests or WebSockets  

## **ASGI (New Standard)**
- Asynchronous  
- Supports WebSockets, streaming, long-lived connections  
- Used by FastAPI  

### Diagram

```
Client → ASGI Server → FastAPI App → Model
```

FastAPI = Async-first framework → ideal for high-throughput inference.

---

# 🚀 4.2 What Is FastAPI?

FastAPI is a modern Python web framework.

### Key features:
- Extremely fast (Starlette + Pydantic)
- Auto-generated documentation (Swagger UI and ReDoc)
- Built-in validation
- Async support
- Easy dependency injection
- Designed for production ML systems

Install:

```bash
pip install fastapi uvicorn
```

---

# 🛣️ 4.3 FastAPI Endpoints

Basic example:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/hello")
def hello():
    return {"message": "Hello FastAPI!"}
```

Run:

```bash
uvicorn main:app --reload
```

---

# 📁 4.4 Path Parameters

```python
@app.get("/user/{user_id}")
def get_user(user_id: int):
    return {"id": user_id}
```

---

# 🔍 4.5 Query Parameters

```python
@app.get("/search")
def search(q: str = "default"):
    return {"query": q}
```

---

# 📦 4.6 POST Request Body (Important for ML)

Use **Pydantic models**:

```python
from pydantic import BaseModel

class Input(BaseModel):
    age: int
    salary: float

@app.post("/predict")
def predict(data: Input):
    return {"prediction": data.age * 2}
```

---

# 🧱 4.7 Pydantic Models (Data Validation)

```python
class Review(BaseModel):
    text: str
    rating: float
```

FastAPI automatically:
- Validates types  
- Rejects bad input  
- Generates documentation  

---

# 🧠 4.8 Loading ML Models Efficiently

Load **once**, not per request:

```python
from fastapi import FastAPI
import joblib

app = FastAPI()
model = joblib.load("model.pkl")  # Load once

@app.post("/predict")
def predict(features: Features):
    pred = model.predict([[features.x1, features.x2]])
    return {"prediction": float(pred[0])}
```

---

# 🖼️ 4.9 Handling Files (Images, Audio)

### Image example:

```python
from fastapi import File, UploadFile

@app.post("/classify-image")
async def classify_image(file: UploadFile = File(...)):
    img_bytes = await file.read()
    # process image
    return {"result": "cat"}
```

---

# ⚡ 4.10 Async vs Sync

FastAPI allows you to write:

- Regular functions (def) → synchronous
- Async functions (async def) → asynchronous

### SYNC (Synchronous) — def
Concept
-Your function handles one request at a time.
- While it’s doing something slow (database, ML model, API call), it waits.

Analogy

- Imagine you have one cashier.
- If they spend time typing something slow, the next customer must wait.

### ASYNC (Asynchronous) — async def
Concept

- The function does NOT block.
- While waiting for a slow operation (I/O), the server handles another request.

Analogy

- Imagine you have self-checkout system.
- If one customer is scanning slowly, the system can handle others.


Use `async` for:
- IO-bound tasks  
- HTTP requests  
- File reads  

Do NOT use `async` for:
- Heavy ML inference  
- CPU operations  

---

# 🔥 4.11 Running FastAPI in Production

Use **Gunicorn + Uvicorn workers**:

```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app --workers 4
```

---

# 🔐 4.12 Environment Variables

Example: `.env`

```
MODEL_PATH=models/xgb.bin
API_KEY=12345
```

Load using `pydantic` or `python-dotenv`.

---

# 🧱 4.13 Config Management

Example config:

```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    model_path: str = "models/model.pkl"
    api_key: str

    class Config:
        env_file = ".env"

settings = Settings()
```

---

# 🧩 4.14 Logging

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("api")

@app.get("/health")
def health():
    logger.info("Health check called")
    return {"status": "ok"}
```

---

# 🚧 4.15 Error Handling

```python
from fastapi import HTTPException

@app.get("/divide")
def divide(x: int, y: int):
    if y == 0:
        raise HTTPException(status_code=400, detail="Cannot divide by zero")
    return {"result": x / y}
```

---

# 🪝 4.16 Middleware

```python
from fastapi import Request

@app.middleware("http")
async def log_requests(request: Request, call_next):
    response = await call_next(request)
    return response
```

---

# 🗂️ 4.17 API Versioning

```
/v1/predict
/v2/predict
```

---

# 🔐 4.18 Authentication

## API Key Example

```python
from fastapi import Header, HTTPException

API_KEY = "12345"

@app.get("/secure")
def secure(x_api_key: str = Header(None)):
    if x_api_key != API_KEY:
        raise HTTPException(status_code=401, detail="Unauthorized")
    return {"message": "OK"}
```

---

# 🤖 4.19 Full ML Inference API (Working Example)

```python
from fastapi import FastAPI
from pydantic import BaseModel
import joblib

app = FastAPI()
model = joblib.load("model.pkl")

class Input(BaseModel):
    sepal_length: float
    sepal_width: float
    petal_length: float
    petal_width: float

@app.post("/v1/predict")
def predict(data: Input):
    X = [[data.sepal_length, data.sepal_width, data.petal_length, data.petal_width]]
    pred = model.predict(X)[0]
    return {"prediction": int(pred)}
```
BaseModel comes from Pydantic, which FastAPI uses for:

#### Request data validation

- FastAPI checks:
    - Are all 4 fields present?
    - Are they floats?
    - Does JSON match the schema?
- If wrong → FastAPI returns automatic error.

#### Automatic documentation (Swagger UI)

- Your model appears in /docs UI so users know what to send.
- 
#### Automatic JSON parsing → Python object
- can use dot notation: eg data.sepal_length
---

# 🖼️ 4.20 Computer Vision Example (Image Classification)

```python
@app.post("/vision/predict")
async def vision(file: UploadFile = File(...)):
    img_bytes = await file.read()
    # preprocess → predict
    return {"label": "dog", "confidence": 0.97}
```

---

# ✍️ 4.21 NLP Example (Sentiment)

```python
@app.post("/sentiment")
def sentiment(text: str):
    # tokenizer + model
    return {"sentiment": "positive"}
```

---

# 🧩 4.22 Good vs Bad ML API Design

## ❌ Bad

```
POST /getSentimentV2Test
{
  "123": "hello"
}
```

## ✔️ Good

```
POST /v1/sentiment
{
  "text": "I love this product!"
}
```

---

# 📝 4.23 Summary

- FastAPI is async, fast, and perfect for ML  
- Use Pydantic for validation  
- Load model ONCE  
- Support file uploads  
- Use proper logging, config, and versioning  
- Follow REST best practices  

---

# 🧪 4.24 Chapter 4 Exercises

1. Create a FastAPI POST endpoint for fraud prediction.  
2. Add API key authentication.  
3. Add logging middleware.  
4. Modify your endpoint to load model only once.  
5. Build a file upload endpoint for image classification.

---

# 🎉 End of Chapter 4
