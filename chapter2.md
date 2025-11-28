📘 Chapter 2 — ML Lifecycle Core Concepts
🧠 2.1 Introduction

The Machine Learning lifecycle is the end-to-end process that turns raw data into a production-deployed model.
MLOps practices ensure the lifecycle is automated, reproducible, scalable, and observable.

This chapter covers:

Data collection

Data validation & quality

Feature engineering

Model training

Model evaluation

Drift

Retraining

Production ML architecture

📍 2.2 Data Collection

Data collection is the foundation of every ML project.
Your model can only be as good as the quality and consistency of the data it receives.

Common Data Sources

SQL/NoSQL databases

Data warehouses (BigQuery, Snowflake)

Data lakes (S3, GCS, ADLS)

REST APIs & Webhooks

Event streams (Kafka)

Application logs

IoT and sensor data

User input or product telemetry

MLOps Requirements

Automation: ETL/ELT tools (Airflow, Prefect)

Versioning: Data snapshots stored via DVC or Delta Lake

Validation: Prevent bad data from entering pipelines

ASCII Diagram
            Raw Data Sources
         ┌───────┬───────────┬────────┐
         │  DB   │   APIs    │   S3    │
         └───┬───┴─────┬─────┴────────┘
             │         │
             ▼         ▼
      Automated Ingestion Pipeline

📍 2.3 Data Quality

Bad data = bad predictions.
Data quality ensures ML systems produce accurate and reliable outputs.

Dimensions of Data Quality
Dimension	Meaning
Completeness	Missing values?
Consistency	Schema aligned?
Accuracy	Is it correct?
Freshness	Up-to-date?
Uniqueness	No duplicates?
Validity	In expected range?
Data Quality Problems

❌ Incorrect labels

❌ Missing or corrupted values

❌ Inconsistent units (e.g., meters vs feet)

❌ Duplicate data

❌ Outliers

Tools

Great Expectations

TensorFlow Data Validation

EvidentlyAI

📍 2.4 Feature Engineering

Feature engineering converts raw data into model-ready inputs.

Common Feature Engineering Techniques

Scaling/Normalization

One-hot encoding / Embeddings

Polynomial features

Aggregations (rolling averages, sums)

Domain-specific extractions

Feature selection (remove uninformative features)

Feature Store

A feature store ensures:

Same features in training & production

Centralized feature definitions

Low-latency retrieval for online inference

Examples:
Feast, Tecton, Vertex Feature Store.

📍 2.5 Model Training

Model training is the process of learning patterns from data.

Training Workflow

Split dataset (train/validation/test)

Select model/algorithm

Tune hyperparameters

Run experiments

Version artifacts

Save trained model

Training in MLOps

Reproducibility via Docker

Automating jobs with orchestration tools

Tracking experiments using MLflow or Weights & Biases

Fully scriptable processes

📍 2.6 Model Evaluation

Different problems require different evaluation metrics.

Classification Metrics

Accuracy

Precision

Recall

F1-score

ROC-AUC

Confusion Matrix

Regression Metrics

RMSE

MAE

R²

Production Metrics

Latency

Throughput

Error rate

Fairness

Robustness

Evaluation Architecture
               ┌─────────────────┐
               │ Model Predictions│
               └───────┬─────────┘
                       ▼
            Compare with Ground Truth
                       ▼
               ┌──────────────┐
               │  Evaluation  │
               └──────────────┘

📍 2.7 Drift (Data, Concept, Model)

Drift is the silent killer of production ML systems.

Types of Drift
1. Data Drift

Input distributions change over time.
Example: shifts in user age demographics.

2. Concept Drift

Relationship between features and target changes.
Example: buying behavior before vs. after COVID.

3. Model Drift

Performance drops due to data or concept drift.

Drift Detection Tools

EvidentlyAI

WhyLabs

Arize AI

📍 2.8 Retraining

Retraining ensures your model stays up-to-date.

Retraining Triggers

Drift detected

Performance degradation

New labeled data

Scheduled retraining

Retraining Pipeline
┌───────────────┐
│    New Data    │
└───────┬────────┘
        ▼
  Data Validation
        ▼
 Feature Engineering
        ▼
     Training
        ▼
   Evaluation
        ▼
 Model Registry
        ▼
 Deployment (CI/CD)


Automated retraining is typically managed by:

Airflow

Kubeflow

Prefect

Argo Workflows

📍 2.9 ML System Architecture (Modern ML Stack)

A real ML system contains far more than just a model.

Core Components
1️⃣ Data Layer

Data ingestion

Data validation

Feature store

2️⃣ Training Layer

Scalable training jobs

Experiment tracking

Model registry

3️⃣ Deployment Layer

Model servers (FastAPI, TensorRT, TorchServe)

Containerization (Docker)

Orchestration (Kubernetes)

4️⃣ Monitoring Layer

Metrics & logs

Drift detection

Alerting

Architecture Diagram
                   ┌───────────────┐
                   │  Data Sources  │
                   └───────┬───────┘
                           ▼
                 Ingestion / ETL / ELT
                           ▼
                    Feature Store
                           ▼
              ┌────────────────────────┐
              │     Training Pipeline  │
              └───────┬────────────────┘
                      ▼
                 Model Registry
                      ▼
               Deployment Service
                      ▼
                 Monitoring Layer
                      ▼
                  Retraining Loop

📍 2.10 Summary Table
Component	Purpose
Data collection	Automate & version data
Data quality	Validate and clean data
Feature engineering	Transform data for ML
Training	Reproducible model creation
Evaluation	Measure performance & fairness
Drift	Detect distribution changes
Retraining	Keep model fresh
Architecture	Production-ready ML system
