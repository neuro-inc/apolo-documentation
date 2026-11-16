# Triton Inference Server

## Overview

[NVIDIA Triton Inference Server](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) serves the models of a model repository over the KServe v2 HTTP/REST and gRPC APIs. The app runs it on a CPU or GPU preset of your project and reads the models from a directory in Apolo Files.

With it you can:

* Serve ONNX, PyTorch, TensorRT, OpenVINO and Python backend models, and ensembles of them, from one model repository
* Call the models from other apps of the project, or from outside the cluster over HTTPS and gRPC with a platform token
* Update the models by changing the files in the repository and reloading them, see [Changing the Models](#changing-the-models)

## Before You Install

Prepare a model repository: a directory in Apolo Files with one sub-directory per model. Each model directory holds a `config.pbtxt` and numbered version directories with the model files:

```
models/
  add_sub/
    config.pbtxt
    1/
      model.py
  resnet/
    config.pbtxt
    1/
      model.onnx
```

The layout and the model configuration are described by NVIDIA: [Model Repository](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_repository.html#repository-layout) and [Model Configuration](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_configuration.html).

Upload the directory from your machine, for example:

```bash
apolo mkdir -p storage:triton/models
apolo cp -r -T ./models storage:triton/models
```

The directory must be in the project where you install the app. A path that does not exist or points to a file is refused. An existing empty directory is accepted: the server starts and serves no models.

To register the path once and pick it in several Triton apps, install [Triton Model Repository](triton-model-repository.md).

## Installing the Triton Inference Server App

1. Open the **Apps** section of the **Apolo Console**
2. Find **Triton Inference Server** in the list of available apps
3. Click **Install**

### 1. Resource Preset

Choose a preset with at least 1 GB of memory; a smaller preset is refused. The shared memory of the server, `/dev/shm`, counts against the memory of the preset, and so do the models loaded on CPU. Pick a GPU preset for models that run on a GPU.

### 2. Triton Server Image

* **Container Image Repository** and **Container Image Tag** – the Triton image. The default is `nvcr.io/nvidia/tritonserver` with the tag `26.10-py3`. An image without a tag is refused: NVIDIA publishes no `latest` tag. NVIDIA publishes the images in the [NGC catalog](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/tritonserver)
* **Image Pull Secret** – only for an image in a private registry. Pick a Docker config or a GitHub Container Registry login through **App Integration**
* **Container Image Pull Policy** – when the node pulls the image

The server is started as `tritonserver --model-repository=/models --http-port=8000 --grpc-port=8001 --metrics-port=8002` followed by the **Server Extra Arguments**. An image can also be given by a `sha256` digest, `nvcr.io/nvidia/tritonserver@sha256:<digest>`, except for an image in the Apolo registry.

### 3. Triton Model Repository

Choose where the models are:

* **App Integration** – pick an installed [Triton Model Repository](triton-model-repository.md) app
* **Repository Path** – type the path, for example `storage:triton/models`

The directory is mounted read-only. The server never writes to it, and a change you make to the files is picked up as described in [Changing the Models](#changing-the-models).

### 4. Public HTTP Endpoint

Turn it on to reach the HTTP/REST API from outside the cluster over HTTPS. It is off by default. Choose the authentication:

* **Apolo Platform Authentication** (default) – a request needs a platform token
* **Custom Authentication** – a middleware of your own, given by its name
* **No Authentication** – the endpoint is open to the internet

### 5. Public gRPC Endpoint

Turn it on to reach the gRPC API from outside the cluster over TLS on port 443. It is off by default and has the same authentication choices as the HTTP endpoint. The two endpoints are independent: turn on one, both or none.

### 6. Advanced Fields

* **Server Extra Arguments** – one argument per entry, appended to the `tritonserver` command, for example `--log-verbose=1`. See [Server Arguments](#server-arguments)
* **Hugging Face Cache** – a directory of Apolo Files, mounted read-write at `/root/.cache/huggingface`; `HF_HOME` points there. Pick a [Hugging Face](hugging-face.md) app through **App Integration** or type the path. For models that download files from the Hugging Face Hub
* **Hugging Face Token** – an Apolo Secret with a Hugging Face token, passed to the server as `HF_TOKEN`. Pick it through **App Integration** or enter it
* **Extra Environment Variables** – variables of the server container, each with a **Variable Name** and a **Variable Value**. They override the variables set by the app, `HF_HOME` and `HF_TOKEN` included. A name must start with a letter or `_` and contain only letters, digits and `_`. Names that start with `AIP_` are refused: they switch the image to another serving mode and the server stops serving the endpoints of the app

### 7. Install

Click **Install**. In the default mode the server loads every model before it starts to listen, so a large repository takes a while. The app is healthy when the server reports it is ready.

## What Is Refused

The app refuses these inputs and does not deploy:

* **A public endpoint without authentication together with explicit model control.** With `--model-control-mode=explicit` any caller of an endpoint can load and unload models, including sending model files in the request. Use platform or custom authentication on every public endpoint you turn on, or leave the mode at its default
* **A model repository outside the project of the app, or one that does not exist**, and a repository path that is a file. The Hugging Face cache path is refused when it is outside the project of the app.
* **Server arguments that move ports or addresses, or change gRPC TLS**: `--http-port`, `--grpc-port`, `--metrics-port`, `--http-address`, `--grpc-address`, `--metrics-address`, `--allow-http`, `--allow-grpc`, `--grpc-use-ssl`, `--grpc-use-ssl-mutual`, `--grpc-server-cert`, `--grpc-server-key` and `--grpc-root-cert`. The app sets the ports, and the endpoints depend on them. `tritonserver` accepts a flag by any unambiguous prefix, so `--http-p=9000` counts as `--http-port=9000`; an ambiguous prefix such as `--http=9000` is refused too
* **Variable references in server arguments**: an argument with `$(` in it. The app does not support them
* **Empty entries in the server arguments**: `tritonserver` stops on them
* **Restricting the `health` API** with `--http-restricted-api` or `--grpc-restricted-protocol`: the probes of the app call it. Restricting other APIs is accepted
* **An image without a tag**, unless it is given by a `sha256` digest. NVIDIA publishes no `latest` tag, so set a tag such as `26.10-py3`
* **A preset with less than 1 GB of memory**

## Server Arguments

`--model-control-mode` decides how models are loaded; see [Model Management](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_management.html). Other arguments are described in the output of `tritonserver --help` of your image.

By default a model that fails to load stops the server: the pod restarts and the app does not become healthy. To serve the models that did load, add:

```
--exit-on-error=false
--strict-readiness=false
```

With `--exit-on-error=false` alone the server stays up but reports that it is not ready, so the app does not become healthy. The state of every model, including the reason a model failed, is returned by `POST /v2/repository/index`.

## Outputs

When the app is healthy, its outputs list two endpoints:

* **Triton Server HTTP Endpoint** – the HTTP/REST API. Inside the project it is the internal URL of the output, on port 8000. With the public endpoint on there is also an `https://` URL on port 443
* **Triton Server gRPC Endpoint** – the gRPC API. Inside the project it is the internal URL of the output, on port 8001, without TLS. With the public endpoint on there is also a host on port 443 with TLS

The public gRPC host also appears among the app endpoints. A browser cannot call gRPC; use it with a gRPC client.

Other apps pick the endpoints through **App Integration**. The server also exposes metrics on port 8002. They are reachable inside the project only and are not an output.

Inside the project the endpoints need no token: anyone in the project can call the models and, in explicit mode, load and unload them.

## Using the Server

### HTTP

The calls below follow the [KServe v2 protocol](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/protocol/README.html). With platform authentication send your platform token in `Authorization`:

```bash
TOKEN=$(apolo config show-token)

curl -H "Authorization: Bearer $TOKEN" https://<external host>/v2/health/ready

curl -X POST -H "Authorization: Bearer $TOKEN" https://<external host>/v2/repository/index

curl -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"inputs": [
        {"name": "INPUT0", "shape": [4], "datatype": "FP32", "data": [1, 2, 3, 4]},
        {"name": "INPUT1", "shape": [4], "datatype": "FP32", "data": [10, 20, 30, 40]}
      ]}' \
  https://<external host>/v2/models/add_sub/infer
```

Without authentication on the endpoint, or from another app inside the project, leave out the header and use the internal URL.

### gRPC

Send the platform token as call metadata named `authorization` with the value `Bearer <token>`. For example with the [Triton Python client](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client/README.html), installed with `pip install "tritonclient[grpc]"`:

```python
import subprocess

import numpy as np
import tritonclient.grpc as grpcclient

token = subprocess.check_output(["apolo", "config", "show-token"], text=True).strip()
headers = {"authorization": f"Bearer {token}"}

client = grpcclient.InferenceServerClient(url="<external gRPC host>:443", ssl=True)
print(client.is_server_ready(headers=headers))

inputs = [
    grpcclient.InferInput("INPUT0", [4], "FP32"),
    grpcclient.InferInput("INPUT1", [4], "FP32"),
]
inputs[0].set_data_from_numpy(np.array([1, 2, 3, 4], dtype=np.float32))
inputs[1].set_data_from_numpy(np.array([10, 20, 30, 40], dtype=np.float32))

result = client.infer("add_sub", inputs, headers=headers)
print(result.as_numpy("OUTPUT0"))
```

Inside the project use the internal URL of the output, port 8001, with `ssl=False` and no header.

A call without a valid platform token is refused before it reaches the server.

The server also answers `grpc.health.v1.Health` on the gRPC port.

## Changing the Models

The server reads the repository when it starts. To pick up changed or added models:

* **Reconfigure the app.** A change that affects the deployment, for example a new server argument or an extra environment variable, restarts the server, and the new server loads the repository again. This works in every mode
* **Use explicit model control.** Add `--model-control-mode=explicit` to the server arguments, keep authentication on the public endpoints, and load or unload models with `POST /v2/repository/models/<model>/load` and `.../unload`, or the gRPC `RepositoryModelLoad` and `RepositoryModelUnload`. In the default mode these calls return an error. See [Model Management](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_management.html) and the [model repository extension](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/protocol/extension_model_repository.html)

The repository is mounted read-only. Change its files in Apolo Files, not through the server.

## Limits

* The server runs as a single instance and there is no autoscaling. An update replaces the pod, so the server is unavailable while the new pod loads the models
* A model that fails to load stops the server, unless you set the arguments in [Server Arguments](#server-arguments)
* The metrics endpoint, port 8002, is available inside the project only
* The model repository and the Hugging Face cache must be in the project of the app

## References

* [Triton Inference Server documentation](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Model repository](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_repository.html)
* [Model management](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_management.html)
* [HTTP/REST and gRPC protocol](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/protocol/README.html)
* [Triton Model Repository](triton-model-repository.md)
* [Installing with the Apolo CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/triton-inference-server.md)
