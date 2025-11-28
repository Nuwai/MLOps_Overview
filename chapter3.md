📘 Chapter 3 — REST & API Fundamentals for ML Engineers
🧠 3.1 Introduction

APIs are the backbone of modern ML deployment.
To deploy machine learning models in production, you must understand:

What APIs are

How HTTP works

How REST is designed

JSON structure

Status codes

Best practices

This chapter gives a beginner-friendly but complete understanding of REST fundamentals from an ML engineering perspective.

📍 3.2 What Is an API?

API = Application Programming Interface
It allows two software systems to communicate.

Simple analogy:

An API is like a restaurant waiter.
You tell the waiter what you want → the waiter returns your order.

In ML:

The client sends input data

The ML API returns model predictions

Examples:

Email validation API

Computer vision inference API

Fraud detection ML service

📍 3.3 What Is HTTP?

HTTP = HyperText Transfer Protocol
It is the language of the web, used when browsers, apps, and servers communicate.

Key concepts:

Client → sends a request

Server → sends a response

ASCII Diagram
Client (User/App/Frontend)
           │
           │ HTTP Request
           ▼
      ML API Server
           │
           │ HTTP Response
           ▼
        Prediction

📍 3.4 HTTP Requests vs Responses
HTTP Request Includes:

URL

Method (GET, POST, etc.)

Headers

Body (for POST/PUT)

Example request:

POST /predict
Content-Type: application/json

{
  "age": 30,
  "income": 50000
}

HTTP Response Includes:

Status code

Headers

Body (JSON, text, image…)

Example response:

200 OK
{
  "prediction": "approved",
  "score": 0.87
}

📍 3.5 HTTP Methods
Method	Meaning	ML Usage
GET	Read data	Health check, metadata
POST	Send data	ML predictions
PUT	Replace data	Model config update
PATCH	Partial update	Updating model parameters
DELETE	Remove resource	Delete model version
Practical Example:

To get predictions, we usually use:

POST /predict


Because we are sending input data, not retrieving static content.

📍 3.6 What Is JSON?

JSON = JavaScript Object Notation
It is the most common format for API data exchange.

Example
{
  "name": "Alice",
  "age": 25,
  "is_member": true
}


JSON is:

Human readable

Language independent

Lightweight

FastAPI automatically parses JSON into Python objects.

📍 3.7 What Is REST?

REST = Representational State Transfer
It is an architectural style for designing clean, scalable APIs.

REST Principles:

Stateless

Client–server separation

Clear resource naming

Standardized HTTP methods

Structured responses

Examples of RESTful endpoints:
GET /users
GET /users/123
POST /users
PUT /users/123
DELETE /users/123


For ML APIs, typical endpoints include:

POST /predict
GET /health
GET /model/info

📍 3.8 REST Best Practices (Important for ML APIs)

Use nouns, not verbs, in URLs

❌ /getPrediction

✔️ /predict

Return JSON

Always return status codes

Use versioning

/v1/predict

Document API with Swagger/OpenAPI (FastAPI auto-generates)

Validate inputs (use Pydantic models)

📍 3.9 API Status Codes
Status	Meaning
200	Success
201	Resource created
400	Bad request (invalid input)
401	Unauthorized
403	Forbidden
404	Not found
409	Conflict
422	Validation error
500	Internal server error
Example (ML prediction success):
200 OK
{
  "prediction": "spam"
}

Example (Bad input):
400 Bad Request
{
  "error": "age must be an integer"
}

📍 3.10 Example REST Flow for ML Prediction
Step-by-step:

1. Client sends input JSON

POST /predict
{
  "review": "This product is great!"
}


2. ML server processes input
Loads model → preprocess → inference → postprocess

3. Server returns prediction

200 OK
{
  "sentiment": "positive",
  "score": 0.92
}

📍 3.11 ML API Example (Simple)

Input:

{
  "x1": 3.2,
  "x2": 1.4
}


Output:

{
  "prediction": 0.73
}

📍 3.12 Good vs Bad API Design (ML Perspective)
❌ Bad API Design
POST /predictModelV3Test
{
  "json_data": "3,1,5"
}


Problems:

Bad naming

No schema

Unclear input type

No versioning

Hard to maintain

✔️ Good API Design
POST /v1/predict
Content-Type: application/json

{
  "sepal_length": 3.0,
  "sepal_width": 1.0,
  "petal_length": 5.0,
  "petal_width": 1.8
}


Benefits:

Clear input schema

Versioned endpoint

Validatable

Easy to document

📍 3.13 Practice Questions

Describe the difference between GET and POST.

Why is JSON widely used in ML APIs?

Write an example HTTP request for predicting house price.

What does “stateless” mean in REST?

List three API status codes you would use in an ML system.

📍 3.14 Exercises
Exercise 1 — Create a REST Endpoint Spec

Write an endpoint definition for:

POST /v1/churn/predict


Include:

Input fields

Expected output

Status codes

Exercise 2 — Identify Problems

Given this API:

POST /getPrediction
{
  "x": "3.2"
}


List all design problems.

Exercise 3 — Draft JSON Schema

Write a JSON request body for a fraud-detection ML model.

📍 3.15 Summary

APIs allow communication between ML models and applications

HTTP on the web uses requests & responses

POST is used for ML prediction

JSON is the standard for ML input/output

REST promotes scalable, clean API design

Knowing API fundamentals is essential before learning FastAPI
