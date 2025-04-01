# BitPredict: Industry-Grade Bitcoin Price Prediction System

BitPredict is an end-to-end industry-grade system that ingests, transforms, and version-controls data (including market and sentiment data), builds and tracks machine learning models for Bitcoin price prediction, and deploys an API served via a scalable, monitored infrastructure. This repository serves as the single source of truth for all components of the system, from data ingestion through model training, CI/CD, deployment, and the user interface.

## Table of Contents
- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Project Directory Structure](#project-directory-structure)
- [Development Checklist](#development-checklist)
  - [1. Data Ingestion & ETL Pipeline](#1-data-ingestion--etl-pipeline)
  - [2. Data Transformation & Feature Engineering](#2-data-transformation--feature-engineering)
  - [3. Data Versioning & Storage](#3-data-versioning--storage)
  - [4. Model Training & Experiment Tracking](#4-model-training--experiment-tracking)
  - [5. API Development & Containerization](#5-api-development--containerization)
  - [6. CI/CD Pipeline](#6-cicd-pipeline)
  - [7. Deployment & Monitoring](#7-deployment--monitoring)
  - [8. Frontend User Interface](#8-frontend-user-interface)
- [Contributing](#contributing)
- [References & Resources](#references--resources)

## Project Overview

BitPredict collects historical and live Bitcoin price data along with sentiment data from social media and news sources. The system cleans and processes this data—computing technical indicators (e.g., SMA, EMA, RSI) and sentiment scores (using VADER and FinBERT)—and merges them into a comprehensive feature set. A hybrid deep learning model (LSTM with attention) is trained and tracked using MLflow, then deployed via a FastAPI backend on a Kubernetes cluster with full CI/CD automation and monitoring. An interactive frontend (built with React or Streamlit) provides end users with real-time predictions and insights.

## Architecture

- **Data Sources:** Market Data APIs, Twitter, and News RSS Feeds.
- **ETL Pipeline:** Python scripts and Apache Airflow for scheduled data ingestion.
- **Data Storage & Versioning:** Raw and processed data stored on cloud/local systems, versioned with DVC.
- **Feature Engineering:** Data cleaning, technical indicator computation, sentiment analysis, and feature aggregation.
- **Model Training:** Hybrid model training with experiment tracking using MLflow.
- **CI/CD Pipeline:** Automated workflows via GitHub Actions, containerization with Docker, and deployment on Kubernetes.
- **Monitoring:** Prometheus & Grafana for performance monitoring and logging.
- **User Interface:** Interactive UI built with React or Streamlit.

## Project Directory Structure

Below is the file structure for the project:

```plaintext
bitpredict/
├── .github/
│   └── workflows/
│       ├── deploy.yaml             # CI/CD workflow for deployment pipeline
│       └── train_model.yaml        # CI/CD workflow for retraining and testing
├── api/                            # Backend API service code
│   ├── Dockerfile                  # Dockerfile to containerize the API
│   ├── main.py                     # FastAPI application code for serving predictions
│   ├── requirements.txt            # API dependencies
│   └── tests/
│       └── test_api.py             # Unit/integration tests for the API
├── dags/                           # Apache Airflow DAGs for scheduling pipelines
│   └── bitcoin_pipeline.py         # DAG that orchestrates data ingestion, transformation, and retraining
├── data/                           # Data storage folder
│   ├── raw/                        # Raw data files (e.g., bitcoin_prices.csv, bitcoin_tweets.csv, bitcoin_news.csv)
│   ├── processed/                  # Processed datasets (e.g., bitcoin_features.csv)
│   └── .gitignore                  # Git ignore file for large files
├── docs/                           # Documentation for the project
│   ├── architecture.md             # Detailed system architecture documentation
│   └── user_manual.md              # User guide and installation instructions
├── model/                          # Model development and training code
│   ├── notebooks/                  # Jupyter notebooks for exploratory analysis and prototyping
│   │   └── exploratory_analysis.ipynb
│   ├── train.py                    # Script to preprocess data, train the model, and log experiments (MLflow)
│   ├── model.py                    # Code defining the model architecture (hybrid LSTM/attention)
│   ├── requirements.txt            # Python dependencies for model training
│   └── utils/                      # Utility functions for ETL, feature engineering, and sentiment analysis
│       ├── etl.py                  # Common ETL functions
│       ├── features.py             # Functions to compute technical indicators
│       └── sentiment.py            # Functions for sentiment analysis using VADER and FinBERT
├── deployment/                     # Deployment configurations and scripts
│   ├── kubernetes/                 # Kubernetes manifests for Deployment, Service, Ingress, HPA, etc.
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── ingress.yaml
│   │   └── hpa.yaml
│   ├── docker-compose.yaml         # Optional: for local multi-container testing
│   └── README.md                   # Deployment instructions and notes
├── dvc.yaml                        # DVC pipeline configuration file (for data versioning and ETL)
├── .dvc/                          # DVC metadata and cache directory
├── environment.yml                 # Conda environment file for reproducibility
├── README.md                       # This project README
└── setup.py                        # (Optional) Packaging script for distribution
```

## Development Checklist

Below is a checklist broken down by major development phases. Use this as your ticket list to track progress.

### 1. Data Ingestion & ETL Pipeline
- **[ ] Ticket 1.1: Setup Market Data Ingestion**
  - Research and register for CoinGecko/CryptoCompare APIs.
  - Develop `price_ingest.py` to fetch OHLC data.
  - Save raw data in `data/raw/bitcoin_prices.csv`.
- **[ ] Ticket 1.2: Setup Sentiment Data Ingestion**
  - Research Twitter API and RSS feeds for news.
  - Develop `sentiment_ingest.py` to fetch tweets and news.
  - Save raw data in `data/raw/bitcoin_tweets.csv` and `data/raw/bitcoin_news.csv`.
- **[ ] Ticket 1.3: Automate Ingestion with Airflow**
  - Create an Airflow DAG in `dags/bitcoin_pipeline.py` to schedule ingestion scripts.
  - Test the DAG to ensure data is updated as scheduled.

### 2. Data Transformation & Feature Engineering
- **[ ] Ticket 2.1: Data Cleaning**
  - Implement cleaning functions in `model/utils/etl.py`.
  - Handle missing values, convert dates, and remove outliers.
- **[ ] Ticket 2.2: Compute Technical Indicators**
  - Implement indicator functions (SMA, EMA, RSI) in `model/utils/features.py`.
- **[ ] Ticket 2.3: Sentiment Analysis**
  - Integrate VADER for tweet sentiment in `model/utils/sentiment.py`.
  - Optionally integrate FinBERT for nuanced sentiment from news headlines.
- **[ ] Ticket 2.4: Merge Features**
  - Create a script to merge price, technical indicators, and sentiment scores.
  - Save the final dataset to `data/processed/bitcoin_features.csv`.

### 3. Data Versioning & Storage
- **[ ] Ticket 3.1: Organize Data Storage**
  - Ensure `data/raw/` and `data/processed/` folders are set up.
- **[ ] Ticket 3.2: Initialize DVC**
  - Run `dvc init` and add processed data to DVC.
  - Commit DVC configuration and add remote storage if applicable.

### 4. Model Training & Experiment Tracking
- **[ ] Ticket 4.1: Develop Model Training Code**
  - Write `model/train.py` to load the final dataset and prepare data sequences.
  - Build a hybrid LSTM/Attention model in `model/model.py`.
- **[ ] Ticket 4.2: Integrate MLflow**
  - Add MLflow experiment tracking to `train.py` (log parameters, metrics, and model).
- **[ ] Ticket 4.3: Exploratory Analysis**
  - Create Jupyter notebooks in `model/notebooks/` for data exploration and model prototyping.

### 5. API Development & Containerization
- **[ ] Ticket 5.1: Develop FastAPI Service**
  - Create `api/main.py` to load the trained model and serve predictions.
  - Write unit tests in `api/tests/test_api.py`.
- **[ ] Ticket 5.2: Create Dockerfile for API**
  - Develop `api/Dockerfile` to containerize the FastAPI application.
- **[ ] Ticket 5.3: Define API Requirements**
  - List all dependencies in `api/requirements.txt`.

### 6. CI/CD Pipeline
- **[ ] Ticket 6.1: Setup GitHub Actions Workflows**
  - Create `.github/workflows/train_model.yaml` for automated model retraining.
  - Create `.github/workflows/deploy.yaml` for building Docker images and deploying the API.
- **[ ] Ticket 6.2: Test CI/CD Pipeline**
  - Validate automated tests, builds, and deployments via GitHub Actions.

### 7. Deployment & Monitoring
- **[ ] Ticket 7.1: Develop Kubernetes Manifests**
  - Create `deployment/kubernetes/deployment.yaml`, `service.yaml`, `ingress.yaml`, and `hpa.yaml`.
- **[ ] Ticket 7.2: Deploy to Kubernetes Cluster**
  - Deploy your API using a managed Kubernetes service or local Minikube.
- **[ ] Ticket 7.3: Set Up Monitoring & Logging**
  - Deploy Prometheus and Grafana for monitoring.
  - Configure centralized logging with Fluentd/ELK.
- **[ ] Ticket 7.4: Configure Alerting and Feedback Loop**
  - Integrate alerting mechanisms to trigger retraining if model performance degrades.

### 8. Frontend User Interface
- **[ ] Ticket 8.1: Develop the UI**
  - Build a frontend using React or Streamlit that displays predictions and performance metrics.
- **[ ] Ticket 8.2: Integrate API with UI**
  - Connect the UI to your FastAPI endpoint for real-time predictions.
- **[ ] Ticket 8.3: Test and Refine UI**
  - Ensure responsiveness, usability, and error handling.

## Contributing

Please follow our contribution guidelines:
- Fork the repository.
- Create a branch for each feature or bugfix.
- Write clear commit messages.
- Submit pull requests with detailed descriptions of your changes.

## References & Resources

- [Apache Airflow Documentation](https://airflow.apache.org/docs/apache-airflow/stable/index.html)
- [DVC Documentation](https://dvc.org/doc)
- [MLflow Documentation](https://mlflow.org/docs/latest/index.html)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Docker Documentation](https://docs.docker.com/get-started/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [VADER Sentiment Analysis](https://github.com/cjhutto/vaderSentiment)
- [Hugging Face Transformers](https://huggingface.co/docs/transformers/)

---

