# Vehicle Insurance MLOps Pipeline

## 📌 Project Overview
The **Vehicle Insurance MLOps Pipeline** is an end-to-end Machine Learning project designed to predict whether a customer will be interested in purchasing vehicle insurance based on their demographic and historical data. This project demonstrates industry-standard MLOps practices, going from data ingestion and model training to containerized deployment on AWS using a secure CI/CD pipeline.

---

## ⚙️ How the Project Works (User Flow)
The application provides a seamless connection between a frontend user interface and a robust ML backend:
1. **Data Input**: A user visits the hosted web application and fills out a form detailing their demographics (Age, Gender, Region) and vehicle information (Damage history, Vintage, Driving License).
2. **Form Submission & Processing**: The FastAPI backend captures the submitted data, validates it, and transforms the raw inputs into a model-compatible `pandas` DataFrame using the preprocessing components.
3. **Model Prediction**: The backend invokes the `VehicleDataClassifier`, which loads the pre-trained classification model. The model calculates the likelihood of the customer purchasing insurance.
4. **Displaying Results**: The resulting prediction is instantaneously relayed back to the users' screen as either `Response-Yes` (likely to buy insurance) or `Response-No` (unlikely to buy).
5. **On-Demand Training**: An administrator can trigger the `/train` endpoint. This runs the `TrainPipeline` asynchronously, which retrieves current data, retrains a new model using `scikit-learn`, evaluates it, and ultimately promotes it for use if accuracy thresholds are met.

---

## ☁️ How the Backend Works with AWS
This project deeply integrates with **Amazon Web Services (AWS)** for robust model storage, containerized application hosting, and seamless continuous deployment workflows:

* **Amazon S3 (Simple Storage Service):** Inside the backend pipeline, `boto3` is utilized to establish a direct connection configuration (`aws_connection.py`, `aws_storage.py`). The MLOps pipeline interfaces with S3 buckets to securely pull raw/processed datasets for training streams, and also to save/load trained Machine Learning artifacts (`model.pkl`) automatically.
* **Amazon ECR (Elastic Container Registry):** Once the CI/CD pipeline verifies code health, a production-ready Docker image containing the FastAPI server and ML ecosystem is built, tagged, and actively pushed to an ECR Repository as defined in the deployment workflow.
* **Amazon EC2 (Elastic Compute Cloud):** A self-hosted GitHub Actions runner connects to an actively running EC2 instance. The EC2 instance securely pulls the latest image directly from ECR.
* **Secure Credential Injection:** During deployment (`aws.yaml`), crucial credentials like `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_DEFAULT_REGION` are dynamically unpacked from GitHub Secrets and securely passed into the Docker container via environmental runtime flags (`-e`). This allows the isolated backend container to securely authenticate internally with S3 and other AWS sub-services without exposing keys in the source code.

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
