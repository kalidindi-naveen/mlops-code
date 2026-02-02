Setup Python3.12 -- Code Work on python 3.12 only
```
brew install python@3.12
```
```
/opt/homebrew/opt/python@3.12/bin/python3.12 -m venv .venv
source .venv/bin/activate
```
Local:
------
Install dependencies:
```
pip install mlflow
```
Start MLFLOW: 
```
mlflow ui --backend-store-uri sqlite:///mlflow.db --port 7006
```
- No need to install sqlite: python buildin module

Browser:
```
http://localhost:7006/
```

K8s Local:
----------
kind create cluster --name mlflow

Community Helm Charts: https://community-charts.github.io/docs/charts/mlflow/basic-installation

```
kind delete clusters --all 
```

Production Ready:
-----------------
1. Create RDS - PostgreSQL (easy create + mlflow123)
2. Laptop to RDS will not Work: Enable Public access
3. Connect to RDS
psql -h mlflow-0599.c6hsky0suvfk.us-east-1.rds.amazonaws.com -p 5432 -U postgres
Password for user postgres:
see list of db: \l 
4. create database 
```
CREATE DATABASE mlflow
CREATE USER mlflow_user WITH PASSWORD 'mlflow_password';
```
5. Grant all permissions to mlflow_user 
```
GRANT ALL PRIVILEGES ON DATABASE mlflow TO mlflow_user;
GRANT ALL PRIVILEGES ON SCHEMA public TO mlflow_user;
```

6. Setup K8s 
https://community-charts.github.io/docs/charts/mlflow/postgresql-backend-installation
```
kubectl create ns mlflow
```
```
helm install mlflow community-charts/mlflow \
  --namespace mlflow \
  --set backendStore.databaseMigration=true \
  --set backendStore.postgres.enabled=true \
  --set backendStore.postgres.host=mlflow-0599.c6hsky0suvfk.us-east-1.rds.amazonaws.com \
  --set backendStore.postgres.port=5432 \
  --set backendStore.postgres.database=mlflow \
  --set backendStore.postgres.user=mlflow_user \
  --set backendStore.postgres.password=mlflow_password
```
7. Create MLFLOW Connector to connect to it

Data Set: https://www.kaggle.com/datasets/yasserh/wine-quality-dataset

Setup DVC:
```
dvc init
dvc remote add -d wine s3://nk-dvc-001
dvc push
```
8. Test Model
```
python3 train.py
```