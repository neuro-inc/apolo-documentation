# Triton Inference Server

### Overview <a href="#overview" id="overview"></a>

[NVIDIA Triton Inference Server](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) serves the models of a model repository in Apolo Files over the KServe v2 HTTP/REST and gRPC APIs. This page describes how to install and manage it with the Apolo CLI. What the app does, what it refuses and how to call it is described in the [Console guide](../../../../apolo-console/apps/installable-apps/available-apps/triton-inference-server.md).

Prepare the model repository first: a directory in Apolo Files, in your project, with one sub-directory per model that holds a `config.pbtxt` and numbered version directories. See [Model Repository](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_repository.html#repository-layout) for the layout.

```bash
apolo mkdir -p storage:triton/models
apolo cp -r -T ./models storage:triton/models
```

### Install with the Apolo CLI <a href="#install" id="install"></a>

**Step 1** — Get the configuration file template:

```bash
apolo app-template get triton-inference-server -f triton.yaml
```

**Step 2** — Fill in the parameters. The smallest configuration: a preset with at least 1 GB of memory and the path of the model repository. The image defaults to `nvcr.io/nvidia/tritonserver:26.10-py3`. Both public endpoints are off, so the server is reachable inside the project only:

```yaml
template_name: triton-inference-server
template_version: latest
display_name: triton
input:
  preset:
    name: cpu-medium
  model_repository:
    files_path:
      path: storage:triton/models
```

A configuration with both public endpoints, a model repository taken from an installed Triton Model Repository app, server arguments and the Hugging Face integrations:

```yaml
template_name: triton-inference-server
template_version: latest
display_name: triton
input:
  preset:
    name: gpu-l4-x1
  image:
    repository: nvcr.io/nvidia/tritonserver
    tag: 26.10-py3
    pull_policy: IfNotPresent
  model_repository:
    type: app-instance-ref
    instance_id: <triton-model-repository-app-id>
    path: $.model_repository
  ingress_http:
    auth:
      type: apolo_auth
  ingress_grpc:
    auth:
      type: apolo_auth
  server_extra_args:
    - --exit-on-error=false
    - --strict-readiness=false
    - --log-verbose=1
  hf_cache:
    files_path:
      path: storage:.apps/hugging-face-cache
  hf_token:
    token_name: my-hf-token
    token:
      key: hf-token
  extra_env_vars:
    - name: OMP_NUM_THREADS
      value: "4"
```

Preset names depend on the cluster; list them with `apolo config show`.

| Parameter | Meaning |
| --- | --- |
| `preset` | Resource preset, at least 1 GB of memory |
| `image` | Triton image: `repository`, `tag` and `pull_policy`, by default `nvcr.io/nvidia/tritonserver:26.10-py3`. A tag or a `sha256` digest is required. `imagepullsecret` is for a private registry |
| `model_repository` | The storage path in `files_path.path`, or a reference to the `model_repository` output of a Triton Model Repository app |
| `ingress_http` | Public HTTP/REST endpoint. Leave it out to keep it off. `auth.type` is `apolo_auth` (default), `custom_auth` or `no_auth` |
| `ingress_grpc` | Public gRPC endpoint on port 443, with the same `auth` choices. Leave it out to keep it off |
| `server_extra_args` | Arguments appended to the `tritonserver` command, one per list item |
| `hf_cache` | Apolo Files directory mounted at `/root/.cache/huggingface` |
| `hf_token` | Hugging Face token: a name and an Apolo Secret key. Passed as `HF_TOKEN` |
| `extra_env_vars` | Environment variables of the server container |

The inputs that are refused are listed in [What Is Refused](../../../../apolo-console/apps/installable-apps/available-apps/triton-inference-server.md#what-is-refused). Among them: `no_auth` on a public endpoint together with `--model-control-mode=explicit`, the arguments `--http-port`, `--grpc-port`, `--metrics-port` and the other port, address and gRPC TLS arguments, a `$(` in an argument, a model repository outside your project or one that does not exist, and a preset with less than 1 GB of memory.

**Step 3** — Install the application:

```bash
apolo app install -f triton.yaml
```

Check its state with `apolo app ls`. The server loads every model before it starts to listen, so a large repository takes a while. Read the logs with:

```bash
apolo app logs <app-id>
```

### Get the endpoints

```bash
apolo app get-values <app-id>
```

The values `http_endpoint` and `grpc_endpoint` hold the endpoints. Each has an `internal_url` and, when the public endpoint is on, an `external_url` with a `host`, a `port` and a `protocol`. The internal HTTP endpoint is on port 8000 and the internal gRPC endpoint on port 8001, without a token. The public endpoints are on port 443.

Call them as described in [Using the Server](../../../../apolo-console/apps/installable-apps/available-apps/triton-inference-server.md#using-the-server). With platform authentication the token comes from the CLI:

```bash
curl -H "Authorization: Bearer $(apolo config show-token)" https://<external host>/v2/health/ready
```

For gRPC send it as call metadata: `authorization: Bearer <token>`.

### Reconfigure

Change the configuration file and apply it to the installed app:

```bash
apolo app configure <app-id> -f triton.yaml
```

The server restarts when the change affects the deployment, for example new server arguments, and loads the model repository again. To see the configuration the app runs with, use `apolo app get-input <app-id>`.

To make the server load models you changed in Apolo Files without a restart, use `--model-control-mode=explicit` together with authentication on the public endpoints, see [Changing the Models](../../../../apolo-console/apps/installable-apps/available-apps/triton-inference-server.md#changing-the-models).

### Uninstall

```bash
apolo app uninstall <app-id>
```

If the server uses a Triton Model Repository app, uninstall the server first and the repository app after it. The platform refuses to uninstall the repository app while the server refers to it, unless you pass `--force`.

## Triton Model Repository <a href="#triton-model-repository" id="triton-model-repository"></a>

The [Triton Model Repository](../../../../apolo-console/apps/installable-apps/available-apps/triton-model-repository.md) app registers the path once, so that the server picks it as an integration. It runs no workload and is healthy right after the install.

```yaml
template_name: triton-model-repository
template_version: HEAD
display_name: triton-models
input:
  model_repository:
    files_path:
      path: storage:triton/models
```

```bash
apolo app install -f triton-models.yaml
apolo app ls
```

The path must be in your project and must be an existing directory; otherwise the install is refused with a message that names the path. Take the app ID from `apolo app ls` and use it as `instance_id` in the `model_repository` reference of the server configuration, as shown above.

`apolo app get-values <app-id>` of this app returns the `model_repository` value with the path.

### References

* [Triton Inference Server in the Apolo Console](../../../../apolo-console/apps/installable-apps/available-apps/triton-inference-server.md)
* [Triton Model Repository in the Apolo Console](../../../../apolo-console/apps/installable-apps/available-apps/triton-model-repository.md)
* [Managing Apps](../managing-apps.md)
* [Triton Inference Server documentation](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
