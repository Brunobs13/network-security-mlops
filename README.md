# Network Security ML Project

Machine learning project for phishing detection with a cloud-focused deployment setup.

## Project Overview
This project includes an end-to-end pipeline that:
- Uses MongoDB and local files as data sources
- Ingests and validates phishing-related data
- Trains and evaluates classification models
- Tracks experiments with MLflow (DagsHub)
- Serves predictions with a FastAPI application
- Publishes Docker images to AWS ECR
- Deploys on a self-hosted runner with GitHub Actions

## Architecture

```text
Developer Push (main)
        |
        v
GitHub Actions (CI/CD)
  - CI job
  - Build Docker image
  - Push image to AWS ECR
        |
        v
Self-hosted Runner (Deployment)
  - Pull latest image
  - Restart API container
        |
        v
FastAPI Service
  - /health
  - /train
  - /predict

Training Pipeline -> Artifacts -> Optional S3 Sync
                       |
                       v
               DagsHub / MLflow
                       |
                       v
                    MongoDB
```

## Main Components
1. Data ingestion (`data_ingestion.py`)
2. Data validation (`data_validation.py`)
3. Data transformation (`data_transformation.py`)
4. Model training (`model_trainer.py`)
5. Batch and API prediction (`batch_prediction.py`, `app.py`)

## Project Structure

```text
networksecurity/
├── .github/workflows/main.yml
├── app.py
├── main.py
├── Dockerfile
├── data_schema/
├── Network_Data/
├── final_model/
├── networksecurity/
│   ├── cloud/
│   ├── components/
│   ├── entity/
│   ├── exception/
│   ├── logging/
│   ├── pipeline/
│   └── utils/
├── pipeline/
├── templates/
├── setup_dvc.sh
└── .env.example
```

## Local Setup

1. Create and activate environment
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

2. Configure environment variables
```bash
cp .env.example .env
```
Set at least:
- `MONGODB_URI`
- `TRAINING_BUCKET_NAME`

3. Run training
```bash
python main.py
```
_or_
```bash
python pipeline/training_pipeline.py
```

4. Run API
```bash
python app.py
```

## Docker
```bash
docker build -t networksecurity:latest .
docker run -d --name networksecurity -p 8000:8000 networksecurity:latest
```

## GitHub Actions / AWS
Workflow uses repository Secrets or Variables such as:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `AWS_REGION`
- `AWS_ECR_LOGIN_URI`

## DVC + DagsHub
```bash
chmod +x setup_dvc.sh
./setup_dvc.sh
```
Required:
- `DAGSHUB_USER`
- `DAGSHUB_TOKEN`

## What This Project Shows
- Practical MLOps structure
- Cloud deployment flow (AWS ECR + self-hosted runner)
- FastAPI model serving
- Experiment tracking with MLflow
- MongoDB-backed ingestion step
