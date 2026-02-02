## Local Testing
```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
```
python3 generate_data.py
python3 train.py
python3 api.py
```
```
dvc init
dvc remote add -d churn_model s3://nk-dvc-001
dvc add data/churn_data.csv
```
```
upload model to s3 manually
```