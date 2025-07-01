# MLflow

### Overview

[MLflow](https://mlflow.org) is the de‑facto open‑source platform for logging experiments, tracking metrics, storing artifacts and registering models. The **MLFlow Core** app on Apolo gives you a production‑ready tracking server with:

* A lightweight web UI
* Flexible metadata backend — SQLite or Postgres&#x20;
* Built‑in artifact storage on Apolo Files
* Optional HTTPS ingress secured by platform auth

### Key Features

| Feature                 | How the Apolo App Helps                                                             |
| ----------------------- | ----------------------------------------------------------------------------------- |
| **Experiment Tracking** | Logs parameters, metrics & code revisions via REST API.                             |
| **Artifact Storage**    | Push model binaries & notebook outputs to an S3‑compatible bucket (`storage:` URI). |
| **Model Registry**      | Register & promote models with the same endpoint.                                   |
| **Pluggable Metadata**  | Choose embedded **SQLite** (PVC) or external **Postgres** DSN.                      |
| **One‑click Scaling**   | Resize resources by switching the _Resource Preset_.                                |
| **Secure Ingress**      | Auto‑generated HTTPS domain with platform auth.                                     |

### Apolo Deployment

There are two ways to deploy the app:

1. **Web Console UI** – click‑through wizard (no YAML).
2. **Apolo CLI** – declarative `app install` command that fits CI/CD.

### Web Console UI

#### 1 · Open the catalogue

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-22 at 15.37.11.png" alt=""><figcaption></figcaption></figure>

#### 2 · Fill the wizard

| Section                 | Field                                                                                                                                      | Example              | Notes                                                  |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | -------------------- | ------------------------------------------------------ |
| **Resource Preset**     | `cpu-large`                                                                                                                                | 4 vCPU / 8 GiB       | Pick any preset.                                       |
| **Enable HTTP Ingress** | `auth = true`                                                                                                                              |  —                   | Creates `https://mlflow‑<id>.apps.<cluster>.apolo.us`. |
| **Metadata Storage**    | <p><em>SQLite</em> – PVC <code>mlflow-sqlite-storage</code><br><em>Postgres</em> – <code>postgresql://user:pwd@host:5432/mlflow</code></p> | Choose one backend.  |                                                        |
| **Artifact Store**      | `storage:mlflow-artifacts`                                                                                                                 | Path in Apolo Files. |                                                        |

\
Click **Install**. Wait until _Status → healthy_ and copy the **External MLFlow URL**.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-22 at 20.35.13.png" alt=""><figcaption></figcaption></figure>

### Apolo CLI

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

