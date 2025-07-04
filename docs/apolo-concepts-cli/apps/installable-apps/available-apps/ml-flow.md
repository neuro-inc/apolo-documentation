# MLflow

## Overview

[MLflow](https://mlflow.org) is the de‑facto open‑source platform for logging experiments, tracking metrics, storing artifacts and registering models. The **MLFlow Core** app on Apolo gives you a production‑ready tracking server with:

* A lightweight web UI
* Flexible metadata backend — SQLite or Postgres&#x20;
* Built‑in artifact storage on Apolo Files
* Optional HTTPS ingress secured by platform auth

For more information about MLFlow, please visit the main [MLFlow ](../../../../apolo-console/apps/installable-apps/available-apps/ml-flow.md)app page.

## Installing

MLFlow can be installed on Apolo either via the CLI or the [Web Console](../../../../apolo-console/apps/installable-apps/available-apps/ml-flow.md). Below are the detailed instructions for installing MLFlow using Apolo CLI.

### Installing via Apolo CLI

The CLI flow mirrors the UI fields but lets you automate everything.

```yaml
# 1. place this in mlflow.yaml

template_name: "mlflow-core"
template_version: "v25.5.0"
input:
  preset:
    name: "cpu-small"
  ingress_http:
    auth: true          # basic‑auth on
  metadata_storage:
    postgres_uri:
      uri: "postgresql://mlflow:Pa55w0rd@postgres-svc:5432/mlflow"
  artifact_store:
    path: "storage:mlflow-artifacts"

# 2. deploy it
apolo app install -f mlflow.yaml \
  --cluster <CLUSTER> --org <ORG> --project <PROJECT>
```

**Explanation**

* `preset.name=cpu-small` – 2 vCPU / 4 GiB (adjust as needed).
* `postgres_uri.uri` – Points to an existing Postgres service; omit this block to fall back to SQLite.
* `ingress_http.auth=true` – Adds basic‑auth to the public URL.

***

### Inputs / Outputs _(schema v1)_

**Inputs**

| JSON Path                           | Default                    | Description                 |
| ----------------------------------- | -------------------------- | --------------------------- |
| `preset.name`                       | –                          | Resource preset.            |
| `ingress_http.auth`                 | `true`                     | Basic‑auth on external URL. |
| `metadata_storage.pvc_name`         | `mlflow-sqlite-storage`    | For SQLite backend.         |
| `metadata_storage.postgres_uri.uri` | `null`                     | Full Postgres DSN.          |
| `artifact_store.path`               | `storage:mlflow-artifacts` | Artifact bucket path.       |

**Outputs** (seen in _Details ▸ Output_)

| Key                      | Purpose                          |
| ------------------------ | -------------------------------- |
| `external_web_app_url.*` | Public UI & REST endpoint.       |
| `external_server_url.*`  | Tracking server URL for clients. |

### Usage

Use the CLI to set the `MLFLOW_TRACKING_TOKEN` with the token from your session (only if you enabled platform auth)

```bash
export MLFLOW_TRACKING_TOKEN=$(apolo config show-token)
```

The only requirement is to expose `MLFLOW_TRACKING_URI` and a basic‑auth header.

```python
import mlflow
from mlflow.tracking import MlflowClient

TRACKING_URI = "https://apolo-taddeus-mlflow-core-d660e438.apps.apolo.us"
mlflow.set_tracking_uri(TRACKING_URI)
mlflow.set_registry_uri(TRACKING_URI)  # optional

client = MlflowClient()
print("Current experiments:")
for exp in client.list_experiments():
    print(exp.name)
```

### References

* [**MLflow Official Documentation**](https://mlflow.org/docs/latest) – Comprehensive guides covering Tracking, Projects, Models, and Registry
* [**MLflow GitHub Repository**](https://github.com/mlflow/mlflow) – Source code, issue tracker, and community discussions

