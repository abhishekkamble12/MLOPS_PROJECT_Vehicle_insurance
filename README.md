# Vehicle Insurance MLOps Pipeline

## 📌 Project Overview
The **Vehicle Insurance MLOps Pipeline** is an end-to-end Machine Learning project designed to predict whether a customer will be interested in purchasing vehicle insurance based on their demographic and historical data. This project demonstrates industry-standard MLOps practices, going from data ingestion and model training to containerized deployment on AWS using a secure CI/CD pipeline.

---

## 🏗️ Architecture & Pipeline Flow
The project is built with a modular architecture encapsulating the complete Machine Learning lifecycle:

1. **Data Ingestion**: Reads data from cloud storage or local databases.
2. **Data Transformation**: Cleans, encodes, and preprocesses the dataset (handling class imbalances using techniques like SMOTE).
3. **Model Training**: Trains the classification model using `scikit-learn`.
4. **Model Evaluation**: Evaluates the trained model against predefined metrics to ensure performance and generalizability.
5. **Model Pusher**: Promotes the validated model into production for serving.

---

## 🛠️ Technology Stack
- **Language**: Python 3.10
- **Web Framework**: FastAPI, Uvicorn (ASGI), Jinja2 (HTML Templating)
- **Machine Learning**: `scikit-learn`, `imblearn`, `pandas`, `numpy`
- **Visualization**: `matplotlib`, `seaborn`, `plotly`
- **Database**: MongoDB (via `pymongo`)
- **Cloud Provider**: Amazon Web Services (AWS)
- **Containerization**: Docker
- **CI/CD**: GitHub Actions

---

## 🚀 CI/CD & Deployment
The deployment process is fully automated via **GitHub Actions** (`.github/workflows/main.yaml`):

1. **Continuous Integration**: On every push to `main`, the code is linted using `flake8` and tested out using `pytest`.
2. **Continuous Delivery**: A Docker image is built and pushed to **Amazon Elastic Container Registry (ECR)**.
3. **Continuous Deployment**: A self-hosted GitHub Actions runner pulls the latest Docker image from ECR and deploys the container onto an **AWS EC2 instance**. Environment variables (AWS Credentials, MongoDB URLs) are securely injected into the container securely via GitHub Secrets.

---

## 🔌 API Endpoints

The web application is exposed via a seamless FastAPI interface with the following core endpoints:

### 1. `GET /`
- **Description:** Renders the main HTML form for users to manually input vehicle insurance application data.
- **Includes Form Fields:** Gender, Age, Driving License, Region Code, Previously Insured, Annual Premium, Policy Sales Channel, Vintage, Vehicle Age, and Damage History.

### 2. `POST /`
- **Description:** Receives the submitted application form, parses the values, transforms the data into a model-compatible format, and returns the machine learning prediction.
- **Output:** Returns either `Response-Yes` (Customer will buy insurance) or `Response-No` (Customer will not buy insurance).

### 3. `GET /train`
- **Description:** Triggers the end-to-end `TrainPipeline`. This runs Data Ingestion, Transformation, Model Training, and Pusher asynchronously behind the scenes.
- **Output:** Returns a success message `Training successful!!!` once the pipeline evaluates and promotes a new version of the ML model.

---

## 💻 Local Development

### 1. Clone the repository
```bash
git clone <repository_url>
cd MLOPS_PROJECT_Vehicle_insurance
```

### 2. Create and Activate Virtual Environment
```bash
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Setup Environment Variables
Create a `.env` file in the root directory and add the necessary configurations:
```ini
AWS_ACCESS_KEY_ID=<your-access-key>
AWS_SECRET_ACCESS_KEY=<your-secret-key>
AWS_DEFAULT_REGION=<your-region>
MONGODB_URL=<your-mongodb-url>
```

### 5. Run the server
```bash
python app.py
```
The server will now run locally on `http://0.0.0.0:5000` (or `http://localhost:5000`).
