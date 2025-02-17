# VS Code

This action runs an instance of [VS Code](https://code.visualstudio.com/) and runs in in the default web browser.

It requires the name of the image on which to run VS Code and references to the following 5 volumes: data, code, config, notebooks, and results. These volumes will be mounted to `/project/data`, `/project/modules`, `/project/config`, `/project/notebooks`, and `/project/results` respectively.

#### Quick example

```
jobs:
  vscode:
    action: gh:apolo-actions/vscode@@v1.0.1
    args:
      volumes_data_remote: $[[ volumes.data.remote ]]
      volumes_code_remote: $[[ volumes.code.remote ]]
      volumes_config_remote: $[[ volumes.config.remote ]]
      volumes_notebooks_remote: $[[ volumes.notebooks.remote ]]
      volumes_results_remote: $[[ volumes.results.remote ]]
```

### Arguments

#### `image`

The name of the image on which to run the VS Code instance. Default is `ghcr.io/neuro-inc/base:latest`. If you use an image that's not derived from `ghcr.io/neuro-inc/base`, make sure it has the [VS Code server](https://github.com/cdr/code-server) installed.

#### Example

```
args:
	image: ghcr.io/neuro-inc/base:latest
```

#### `job_name`

Predictable subdomain name that will replace the job's ID in the full job URI. `""` by default

#### Example

```
args:
	job_name: "vscode-job"
```

#### `http_port`

HTTP port to use for VS Code. `"8080"` by default.

#### Example

```
args:
	http_port: 8282
```

#### `http_auth`

Whether to use HTTP authentication for VS Code Web UI or not. `"True"` by default.

#### Example

```
args:
	http_auth: "false"
```

#### `volumes_data_remote`

Reference to a data volume.

#### Example

```
args:
	volumes_data_remote: $[[ volumes.data.remote ]]
```

#### `volumes_code_remote`

Reference to a code volume.

#### Example

```
args:
	volumes_code_remote: $[[ volumes.code.remote ]]
```

#### `volumes_config_remote`

Reference to a config volume.

#### Example

```
args:
	volumes_config_remote: $[[ volumes.config.remote ]]
```

#### `volumes_notebooks_remote`

Reference to a notebooks volume.

#### Example

```
args:
	volumes_notebooks_remote: $[[ volumes.notebooks.remote ]]
```

#### `volumes_results_remote`

Reference to a results volume.

#### Example

```
args:
	volumes_results_remote: $[[ volumes.results.remote ]]
```

{% hint style="info" %}
Feel free to check the [VS Code action repository](https://github.com/apolo-actions/vscode).
{% endhint %}
