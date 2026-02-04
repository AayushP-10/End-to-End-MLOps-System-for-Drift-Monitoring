
```
## End-to-End MLOps System for Drift Monitoring

![Docker Compose](https://img.shields.io/badge/docker-compose-blue)
![Python](https://img.shields.io/badge/python-3.10-blue)
![License](https://img.shields.io/badge/license-MIT-green)

A production-style MLOps pipeline focused on detecting data drift, retraining machine learning models, and tracking experiments using Airflow, FastAPI, Docker, and MLflow.

---

## Table of Contents

- Project Overview
- Architecture
- Setup Instructions
- Usage
- Monitoring and Retraining
- Project Structure
- Technologies Used
- License

---

## Project Overview

This repository implements an end-to-end MLOps workflow for monitoring data drift and automatically retraining models when drift is detected. The pipeline includes:

- A fully containerized local environment using Docker
- Model training, evaluation, and experiment tracking with MLflow
- Data drift detection powered by Evidently
- Workflow scheduling and automation via Apache Airflow
- A REST API for real-time predictions built with FastAPI

---

## Architecture

The project follows a modular and scalable MLOps architecture:

- data contains datasets
- src/training contains model training logic
- src/drift_detection contains drift monitoring logic
- src/api exposes the prediction service
- airflow/dags defines Airflow workflows
- mlruns stores MLflow experiment artifacts
- reports contains Evidently HTML reports
- docker-compose.yml defines API and MLflow services
- docker-compose.airflow.yml defines Airflow orchestration

---

## Technologies Used

- Python 3.10
- Pandas and Scikit-learn
- Evidently AI
- FastAPI
- MLflow
- Docker and Docker Compose
- Apache Airflow

---

## How to Run Locally

### 1. Clone the repository

git clone https://github.com/your-user/mlops-drift-project.git  
cd mlops-drift-project

### 2. Start all services

.\start.ps1

### 3. Access the services

FastAPI: http://localhost:8000/docs  
MLflow UI: http://localhost:5000  
Airflow UI: http://localhost:8080  

Airflow credentials:  
Username: admin  
Password: admin  

### 4. Run drift monitoring manually

docker-compose exec api python auto_monitor.py

---

## Usage Workflow

1. Initial training  
A logistic regression model is trained using bank.csv.  
Model artifacts and metrics are logged to MLflow.

2. Drift detection  
Incoming data is compared against a reference dataset.  
Feature-level drift is evaluated using Evidently.

3. Retraining and versioning  
If drift thresholds are exceeded, retraining is triggered.  
A new MLflow run is logged automatically.

4. Airflow orchestration  
Pipelines can be scheduled or triggered using Airflow DAGs.  
Supports recurring monitoring and retraining.

5. Prediction API  
FastAPI serves the latest trained model.  
Accepts JSON input for real-time predictions.

---

## Project Structure

mlops-drift-project/
├── airflow/
│   ├── dags/
│   │   └── mlops_dag.py
│   ├── api/
│   │   └── main.py
│   ├── requirements.txt
│   └── Dockerfile
├── data/
│   ├── bank.csv
│   └── bank.zip
├── mlruns/
│   └── models/
│       └── random_forest.pkl
├── reports/
│   ├── drift_auto_report*.html
│   └── drift_report*.html
├── src/
│   ├── drift_detection/
│   │   └── monitor_drift.py
│   ├── inference/
│   └── training/
│       └── train_model.py
├── tests/
│   ├── test_api.py
│   └── test_training.py
├── docker-compose.yml
├── docker-compose.airflow.yml
├── Dockerfile
├── README.md
├── requirements.txt
└── scripts/
    ├── start.ps1
    ├── stop.ps1
    ├── restart.ps1
    ├── rebuild.ps1
    └── reset_db.ps1

---

## Installation

Docker and Docker Compose are required.

### Start the full stack

.\start.ps1

This will:
- Build Docker images
- Start API, MLflow, Airflow, Redis, and PostgreSQL
- Initialize the Airflow database if needed

Optional database reset:

.\reset_db.ps1

---

## Usage

Available services:

FastAPI: http://localhost:8000/docs  
MLflow UI: http://localhost:5000  
Airflow UI: http://localhost:8080  

### Manual Drift Detection

docker-compose exec api python src/drift_detection/monitor_drift.py

Reports are saved in the reports directory.

### Automated Drift Monitoring and Retraining

docker-compose exec api python src/drift_detection/auto_monitor.py

If drift is detected:
- A drift report is generated
- The model is retrained
- A new MLflow run is logged

### Airflow Scheduling

Enable the drift_monitoring_pipeline DAG in the Airflow UI.  
Trigger manually or wait for the schedule.

---

## FastAPI Example

Swagger UI:  
http://localhost:8000/docs

Sample request:

curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{
        "age": 35,
        "job": "technician",
        "marital": "married",
        "education": "secondary",
        "default": "no",
        "housing": "yes",
        "loan": "no",
        "contact": "cellular",
        "month": "may",
        "day_of_week": "mon",
        "duration": 150,
        "campaign": 1,
        "pdays": 999,
        "previous": 0,
        "poutcome": "nonexistent",
        "emp.var.rate": 1.1,
        "cons.price.idx": 93.994,
        "cons.conf.idx": -36.4,
        "euribor3m": 4.857,
        "nr.employed": 5191
      }'

The API returns the predicted class and probability.

---

## Configuration

### Environment Variables

MLFLOW_TRACKING_URI: http://mlflow:5000  
API_PORT: 8000  
AIRFLOW__CORE__EXECUTOR: CeleryExecutor  
AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow

### Ports Used

8000 FastAPI  
5000 MLflow  
8080 Airflow  
8793 Airflow Worker  
5432 PostgreSQL  
6379 Redis  

---