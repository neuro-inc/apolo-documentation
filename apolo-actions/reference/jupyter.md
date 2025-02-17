# Jupyter

This is an [apolo-flow](https://docs.apolo.us/apolo-flow-reference) action launching an instance of [Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/stable/) or [Jupyter Lab](https://jupyterlab.readthedocs.io/en/stable/). It's intended to be used with the Apolo [platform template](https://github.com/neuro-inc/cookiecutter-neuro-project), but can be adapted for other use cases as well.

It requires the references to 5 volumes: data, code, config, notebooks and results. These volumes will be mounted to `/project/data`, `/project/modules`, `/project/config`, `/project/notebooks`, and `/project/results` respectively.

By default, this action will use the `ghcr.io/neuro-inc/base:latest` image to run Jupyter.

After the Jupyter instance is launched, its Web UI will be automatically opened in the default browser.

#### Quick example:

```
jobs:
  jupyter:
    action: gh:apolo-actions/jupyter@master
    args:
      volumes_data_remote: ${{ volumes.data.remote }}
      volumes_code_remote: ${{ volumes.code.remote }}
      volumes_config_remote: ${{ volumes.config.remote }}
      volumes_notebooks_remote: ${{ volumes.notebooks.remote }}
      volumes_results_remote: ${{ volumes.results.remote }}
```

### Arguments

#### `volumes_data_remote`

Reference to a data volume

#### Example

```
args:
	volumes_data_remote: ${{ volumes.data.remote }}
```

#### `volumes_code_remote`

Reference to a code volume

#### Example

```
args:
	volumes_code_remote: ${{ volumes.code.remote }}
```

#### `volumes_config_remote`

Reference to a config volume

#### Example

```
args:
	volumes_config_remote: ${{ volumes.config.remote }}
```

#### `volumes_notebooks_remote`

Reference to a notebooks volume

#### Example

```
args:
	volumes_notebooks_remote: ${{ volumes.notebooks.remote }}
```

#### `volumes_results_remote`

Reference to a results volume

#### Example

```
args:
	volumes_results_remote: ${{ volumes.results.remote }}
```

#### `preset`

Resource preset to use when running the Jupyter job. `""` by default.

#### Example

```
args:
    preset: cpu-small
```

#### `jupyter_mode`

The mode in which to run Jupyter - `"notebook"` or `"lab"`. Uses `"notebook"` by default.

#### Example

```
args:
    jupyter_mode: "lab"
```

#### `job_name`

Predictable subdomain name which replaces the job's ID in the full job URI. `""` by default.

#### Example

```
args:
	job_name: "jupyter-job"
```

#### `multi_args`

Additional arguments. `""` by default.

#### `http_port`

HTTP port to use for Jupyter. `"8888"` by default.

#### Example

```
args:
    http_port: "4444"
```

#### `http_auth`

Whether to use HTTP authentication for Jupyter or not. `"True"` by default.

#### Example

```
args:
    http_auth: "False"
```

{% hint style="info" %}
Feel free to check the [Jupyter action repository](https://github.com/apolo-actions/jupyter).
{% endhint %}
