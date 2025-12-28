## Databricks MLFow

### Get list of experiment id -s
```python
from mlflow.tracking import MlflowClient

all_experiment_ids = []

client = MlflowClient()
all_experiments = client.search_experiments()   
for exp in all_experiments:
    print(f"{exp.experiment_id} {exp.name} {exp._artifact_location}")
    all_experiment_ids.append(exp.experiment_id) 
```

### Get MLFlow runId artefacts

```python
from mlflow.store.artifact.runs_artifact_repo import RunsArtifactRepository
RunsArtifactRepository("runs:/44d40c3e551f498d9810631a588a5a46").list_artifacts('model')
```

### Get runId properties
```python
mlflow.get_run('44d40c3e551f498d9810631a588a5a46').to_dictionary()
```
### Zip artefacts

```python

import mlflow
import os
import zipfile

run_id = "50a2ccb6679f4ff2975d37955fc4c9b3"
artifact_path = "main_model" 

local_path = mlflow.artifacts.download_artifacts(run_id=run_id, artifact_path=artifact_path, dst_path="models/main_model")

output_zip_file = "main_model_artifact.zip"
folder_to_zip = "models/main_model"   

with zipfile.ZipFile(output_zip_file, 'w') as zipf:
    for root, dirs, files in os.walk(folder_to_zip):
        for file in files:
            file_path = os.path.join(root, file)
            arcname = os.path.relpath(file_path, folder_to_zip)
            zipf.write(file_path, arcname)

```


### Pass spark session to external python script

Example MLproject.yml

```yml
name: mlflow_test
python_version: "3.11.11"

entry_points:
  main:
    parameters:
      cluster_id: {type: string}
    command: "python main.py --cluster_id {cluster_id}"    
```

main.py

```python
import argparse
import sys

from databricks.connect import DatabricksSession
from databricks.sdk import WorkspaceClient

parser = argparse.ArgumentParser()
parser.add_argument("--cluster_id", type=str)
args = parser.parse_args()

wc = WorkspaceClient()

spark = DatabricksSession.builder.remote(
    host = wc.config.host,
    token = wc.config.token,
    cluster_id = args.cluster_id
).getOrCreate()

print(f"{spark.version}")

test = spark.createDataFrame([('A','B')])
print(test.show())
```

Notebook where we run mlflow

```python

import mlflow

experiment_name = "/Workspace/Users/some_user/plk/my_mlflow_test"
                  
params = {"cluster_id": "0312-124905-kf00kdcy"}

mlflow.projects.run (
    uri='.',
    experiment_name=experiment_name,
    env_manager="local",
    parameters=params
)

```

### Shift a model version within the MLflow Model Registry

To shift model version 'model_version' within the MLflow Model Registry for the model 'model' from the Staging stage to the Production stage using the MLflow Client, archive any existing model versions already in the Production stage:

```python
from mlflow.tracking import MlflowClient

client.transition_model_version_stage (
name=model,
version=model_version,
stage=“Production“,
archive_existing_versions=True )

```

### Update registered model description

```python
client = MlflowClient() 
client.update_registered_model( name="model name", description="some description" )
```


### Retrieve all active MLflow Model Registry Webhooks for a specific model

```python
from mlflow.utils.rest_utils import http_request

endpoint = “/api/1.0/mlflow/registry-webhooks/list?model_name=model”
response = http_request(host_creds=host_creds, endpoint=endpoint, method=”GET”)

```


### Run batch prediction

Example how to deploy the model using a batch deployment approach and store the predictions in a Delta table for later use.

```python
from mlflow import pyfunc

model = pyfunc.load_model(model_uri=“models:/my-model/1“)
df = spark.read.format(“delta“).load(“/path/to/input_data“)
predictions = model.predict(df)
predictions.write.format(“delta“).save(“/path/to/output_data“)
```