

# ⚡ Sensor Fault Detection System

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?style=for-the-badge&logo=python&logoColor=white)
![Framework](https://img.shields.io/badge/Framework-Flask%20%7C%20FastAPI-blue?style=for-the-badge)
![Scikit-Learn](https://img.shields.io/badge/ML-Scikit--Learn%20%7C%20XGBoost-blue?style=for-the-badge&logo=scikit-learn&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-ECR%20%7C%20EC2%20%7C%20S3-blue?style=for-the-badge&logo=amazon-aws&logoColor=white)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-black?style=for-the-badge&logo=github-actions&logoColor=white)

---

## 📌 Executive Summary

Industrial environments rely heavily on thousands of sensors monitoring temperature, pressure, vibration, and air flow. A single undetected sensor malfunction can lead to severe equipment downtime, costly repairs, or catastrophic system failure.

The **Sensor Fault Detection System** is an enterprise-grade, end-to-end Machine Learning solution designed to monitor, identify, and predict sensor anomalies in real-time. Built strictly adhering to modular software engineering principles, the system includes automated schema validation, custom error logging, dynamic model selection, artifact tracking via AWS S3, and automated continuous deployment using Docker, AWS ECR, AWS EC2, and GitHub Actions.

---

## 📐 System Architecture & End-to-End Workflow

The following architecture demonstrates the complete path from raw sensor data ingestion to cloud deployment:

```text
+---------------------------------------------------------------------------------+
|                                 DATA INGESTION                                  |
|  - Fetch Raw Data from MongoDB / AWS S3                                         |
|  - Perform Train-Test Split & Export Raw Artifacts                              |
+---------------------------------------------------------------------------------+
                                       |
                                       v
+---------------------------------------------------------------------------------+
|                                DATA VALIDATION                                  |
|  - Validate Schema against schema.yaml (Column Count, Types, Regex Rules)      |
|  - Detect & Isolate Bad Data Files to Prevent Pipeline Failure                  |
+---------------------------------------------------------------------------------+
                                       |
                                       v
+---------------------------------------------------------------------------------+
|                              DATA TRANSFORMATION                                |
|  - Missing Value Imputation (KNNImputer / Robust Scaler)                        |
|  - Class Imbalance Mitigation (SMOTE / Class Weighting)                         |
|  - Save Preprocessing Object (.pkl)                                             |
+---------------------------------------------------------------------------------+
                                       |
                                       v
+---------------------------------------------------------------------------------+
|                           MODEL TRAINING & EVALUATION                           |
|  - Train Multiple Models (XGBoost, Random Forest, Gradient Boosting)            |
|  - Hyperparameter Tuning & Performance Threshold Check (F1-Score / ROC-AUC)    |
|  - Register Best Model to AWS S3 / Saved Models Folder                          |
+---------------------------------------------------------------------------------+
                                       |
                                       v
+---------------------------------------------------------------------------------+
|                           DEPLOYMENT & INFERENCE                                |
|  - Flask / FastAPI REST API for Batch Prediction                                |
|  - Containerization via Docker                                                  |
|  - CI/CD via GitHub Actions -> AWS ECR -> AWS EC2                               |
+---------------------------------------------------------------------------------+

```

---

## 🔬 In-Depth Module Breakdown

### 1. Data Ingestion (`src/components/data_ingestion.py`)

* Reads sensor logs from external sources (Database/CSV).
* Performs stratification and exports train/test datasets into the `artifact/data_ingestion` workspace.

### 2. Data Validation (`src/components/data_validation.py`)

* Compares data schema against predefined constraints in `config/schema.yaml`.
* Checks column counts, correct column names, data type drift, and null-value percentages.
* Quarantines non-conforming data directly to a bad-data repository.

### 3. Data Transformation (`src/components/data_transformation.py`)

* Uses **KNNImputer** to estimate missing sensor parameters based on neighboring feature values.
* Applies **StandardScaler / RobustScaler** to eliminate feature scale variations across sensors.
* Implements **SMOTE (Synthetic Minority Over-sampling Technique)** to balance extreme minority fault classes.
* Saves the fitted preprocessor pipeline as `preprocessor.pkl`.

### 4. Model Trainer (`src/components/model_trainer.py`)

* Evaluates candidate classifiers: XGBoost, CatBoost, Random Forest, Logistic Regression.
* Evaluates model performance using **F1-Score, Precision, Recall, and ROC-AUC**.
* Enforces a strict performance standard (e.g., Accuracy > 0.80 and low False Negatives).
* Saves the winning model artifact as `model.pkl` and syncs with AWS S3.

### 5. Exception Handling & Logging

* **`exception.py`**: Intercepts standard Python exceptions, extracts line numbers, file names, and generates detailed stack traces.
* **`logger.py`**: Creates time-stamped log records for every stage in the pipeline to enable easy debugging in production.

---

## 📂 Project Directory Structure

```text
sensor-fault-detection/
│
├── .github/
│   └── workflows/
│       └── main.yaml             # GitHub Actions CI/CD configuration
│
├── config/
│   └── schema.yaml               # Expected schema definition and constraints
│
├── artifact/                     # Generated data splits, preprocessor & model outputs
├── saved_models/                 # Production-ready serialized models (.pkl)
│
├── src/
│   ├── __init__.py
│   ├── logger.py                 # Custom logging configuration
│   ├── exception.py              # Custom error handling module
│   │
│   ├── components/               # Core pipeline stages
│   │   ├── __init__.py
│   │   ├── data_ingestion.py
│   │   ├── data_validation.py
│   │   ├── data_transformation.py
│   │   ├── model_trainer.py
│   │   └── model_evaluation.py
│   │
│   ├── pipeline/                 # Workflow execution handlers
│   │   ├── __init__.py
│   │   ├── train_pipeline.py
│   │   └── predict_pipeline.py
│   │
│   └── utils/                    # Common helper functions (S3 sync, serialization)
│       ├── __init__.py
│       └── main_utils.py
│
├── templates/                    # Web Interface templates
│   └── index.html
│
├── Dockerfile                    # Container configuration file
├── app.py                        # REST API / Web Application entrypoint
├── main.py                       # Local execution trigger script
├── requirements.txt              # Production dependencies
├── setup.py                      # Package installation script
└── README.md                     # Project documentation

```

---

## 🛠️ Tech Stack & Infrastructure

* **Programming Language:** Python 3.8+
* **Machine Learning Libraries:** Scikit-Learn, XGBoost, CatBoost, Imbalanced-Learn, Pandas, NumPy
* **Web Framework:** Flask / FastAPI
* **Packaging:** Setuptools
* **Containerization:** Docker
* **Cloud Infrastructure (AWS):**
* **AWS S3:** Storage bucket for data artifacts and model registry.
* **AWS ECR:** Private container repository.
* **AWS EC2:** Cloud server hosting the live prediction service.


* **CI/CD Automation:** GitHub Actions

---

## 🚀 Getting Started (Local Setup)

### Prerequisites

Ensure you have the following installed:

* [Python 3.8+](https://www.python.org/)
* [Git](https://git-scm.com/)
* [Docker Desktop](https://www.docker.com/) (Optional for container testing)

---

### Step-by-Step Local Setup

1. **Clone the Repository:**
```bash
git clone [https://github.com/ahmedrajakhan/sensor-fault-detection.git](https://github.com/ahmedrajakhan/sensor-fault-detection.git)
cd sensor-fault-detection

```


2. **Create and Activate a Virtual Environment:**
* **Linux / macOS:**
```bash
python3 -m venv venv
source venv/bin/activate

```


* **Windows:**
```bash
python -m venv venv
venv\Scripts\activate

```




3. **Install Dependencies:**
```bash
pip install -r requirements.txt

```


4. **Trigger Training Pipeline:**
```bash
python main.py

```


5. **Start Web API Application:**
```bash
python app.py

```


Access the web interface at: `http://localhost:8080`

---

## 🐳 Docker Deployment

To build and run the application locally within a Docker container:

1. **Build Docker Image:**
```bash
docker build -t sensor-fault-detection:latest .

```


2. **Run Container:**
```bash
docker run -p 8080:8080 sensor-fault-detection:latest

```



---

## ☁️ Continuous Integration & Deployment (AWS CI/CD)

The project leverages **GitHub Actions** for continuous integration and deployment directly to an **AWS EC2** instance via **AWS ECR**.

```text
[ Git Push to Main ] ---> [ GitHub Actions Runner ] ---> [ Build Docker Image ]
                                                                 |
                                                                 v
[ Run App on EC2 ] <--- [ Pull Container Image ] <--- [ Push to AWS ECR ]

```

### AWS Deployment Workflow Setup

1. **AWS Setup:**
* Create an **IAM User** with access to `AmazonEC2ContainerRegistryFullAccess` and `AmazonEC2FullAccess`.
* Create an **ECR Repository** named `sensor-fault-detection`.
* Launch an **AWS EC2 Instance** (Ubuntu), install Docker, and register it as a **GitHub Self-Hosted Runner**.


2. **Configure GitHub Repository Secrets:**
Add the following variables under **Settings > Secrets and Variables > Actions**:
* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_REGION`
* `AWS_ECR_LOGIN_URI`
* `ECR_REPOSITORY_NAME` (`sensor-fault-detection`)



---

## 📄 License

Distributed under the MIT License. See `LICENSE` for more information.

---

### 👤 Author

**Ahmed Raja Khan**

* **GitHub:** [@ahmedrajakhan](https://www.google.com/search?q=https://github.com/ahmedrajakhan)
* **Repository Link:** [sensor-fault-detection](https://github.com/ahmedrajakhan/sensor-fault-detection)

```

```
