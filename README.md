# MLOps-Production-Pipeline

An end-to-end MLOps implementation covering the complete model lifecycle — data ingestion, experiment tracking with MLflow, containerization with Docker, and automated deployment to AWS EC2 via GitHub Actions CI/CD.

---


## 🛠️ Tech Stack

| Layer | Tools |
|-------|-------|
| **ML Model** | scikit-learn (ElasticNet) |
| **Experiment Tracking** | MLflow + DagsHub |
| **API** | Flask |
| **Containerization** | Docker |
| **CI/CD** | GitHub Actions |
| **Cloud** | AWS EC2 + AWS ECR |
| **Data** | UCI Wine Quality Dataset |

---

## 📁 Project Structure

```
├── src/mlProject/
│   ├── components/          # Data ingestion, validation, transformation, training, evaluation
│   ├── pipeline/            # Stage-wise training pipelines
│   ├── config/              # Configuration manager
│   ├── entity/              # Dataclasses for configs
│   ├── constants/           # Path constants
│   └── utils/               # Common utilities
├── config/
│   └── config.yaml          # Project configuration
├── params.yaml              # Model hyperparameters
├── schema.yaml              # Data schema
├── main.py                  # Training pipeline runner
├── app.py                   # Flask application
├── Dockerfile               # Container definition
├── requirements.txt         # Python dependencies
└── .github/workflows/       # CI/CD pipeline
```

---

## ⚙️ ML Pipeline Workflows

Each component follows this update sequence:

```
config.yaml → schema.yaml → params.yaml → entity
     → configuration manager → components → pipeline → main.py → app.py
```

**Stages:**
1. Data Ingestion
2. Data Validation
3. Data Transformation
4. Model Training
5. Model Evaluation (MLflow tracked)

---

## 🚀 Getting Started (Local)

**Clone the repository**
```bash
git clone https://github.com/your-username/mlops-cicd-pipeline.git
cd mlops-cicd-pipeline
```

**Create and activate conda environment**
```bash
conda create -n venv python=3.8 -y
conda activate venv
```

**Install dependencies**
```bash
pip install -r requirements.txt
```

**Run training pipeline**
```bash
python main.py
```

**Start the Flask app**
```bash
python app.py
```

Open your browser at `http://localhost:8080`

---

## 📊 MLflow Experiment Tracking

This project uses [MLflow](https://mlflow.org/docs/latest/index.html) with [DagsHub](https://dagshub.com/) for experiment tracking.

**Run MLflow UI locally:**
```bash
mlflow ui
```

**Set tracking credentials (Linux/Mac):**
```bash
export MLFLOW_TRACKING_URI=https://dagshub.com/<username>/<repo>.mlflow
export MLFLOW_TRACKING_USERNAME=<your_username>
export MLFLOW_TRACKING_PASSWORD=<your_token>
```

**Set tracking credentials (Windows CMD):**
```bash
set MLFLOW_TRACKING_URI=https://dagshub.com/<username>/<repo>.mlflow
set MLFLOW_TRACKING_USERNAME=<your_username>
set MLFLOW_TRACKING_PASSWORD=<your_token>
```

**What MLflow tracks:**
- All training experiments and runs
- Model parameters (alpha, l1_ratio)
- Evaluation metrics (RMSE, MAE, R²)
- Model artifacts and versioning

---

## ☁️ AWS CI/CD Deployment

### Architecture

```
GitHub Push → GitHub Actions → Build Docker Image
    → Push to AWS ECR → Pull on AWS EC2 → Run Container → App Live
```

### Step-by-Step Deployment Setup

**1. Create IAM User**

Go to AWS Console → IAM → Create user with these policies:
- `AmazonEC2ContainerRegistryFullAccess`
- `AmazonEC2FullAccess`

**2. Create ECR Repository**

```
AWS Console → ECR → Create repository → Save the URI
Example URI: 123456789.dkr.ecr.ap-south-1.amazonaws.com/mlproject
```

**3. Launch EC2 Instance (Ubuntu)**

```
AWS Console → EC2 → Launch instance → Ubuntu → t2.micro (free tier)
```

**4. Install Docker on EC2**

SSH into your EC2 instance and run:
```bash
sudo apt-get update -y
sudo apt-get upgrade -y

curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker
```

**5. Configure EC2 as GitHub Self-Hosted Runner**

```
GitHub repo → Settings → Actions → Runners → New self-hosted runner
→ Select Linux → Run the commands shown, one by one on your EC2
```

**6. Add GitHub Secrets**

```
GitHub repo → Settings → Secrets and variables → Actions → New secret
```

| Secret | Value |
|--------|-------|
| `AWS_ACCESS_KEY_ID` | Your IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | Your IAM user secret key |
| `AWS_REGION` | e.g. `ap-south-1` |
| `AWS_ECR_LOGIN_URI` | e.g. `123456789.dkr.ecr.ap-south-1.amazonaws.com` |
| `ECR_REPOSITORY_NAME` | Your ECR repo name |

**7. Push to main branch — deployment is automatic ✅**

---

## 🧠 Model Details

- **Algorithm:** ElasticNet Regression
- **Dataset:** [UCI Wine Quality Dataset](https://archive.ics.uci.edu/ml/datasets/wine+quality)
- **Features:** 11 physicochemical properties (fixed acidity, volatile acidity, citric acid, residual sugar, chlorides, free SO₂, total SO₂, density, pH, sulphates, alcohol)
- **Target:** Wine quality score (0–10)

---

