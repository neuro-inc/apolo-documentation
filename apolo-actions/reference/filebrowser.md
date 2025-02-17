# Filebrowser

This is an [apolo-flow](https://docs.apolo.us/apolo-flow-reference) action launching the [Filebrowser](https://hub.docker.com/r/filebrowser/filebrowser) app for working with platform storage.

All it needs is a reference to a storage folder that becomes the root folder for the Filebrowser instance.

#### Quick example

```
jobs:
  filebrowser:
    action: gh:apolo-actions/filebrowser@v1.0.1
    args:
      volumes_project_remote: $[[ volumes.project.remote ]]
```

### Arguments

#### `volumes_project_remote`

Reference to the project's remote volume.

#### Example

```
args:
	volumes_project_remote: $[[ volumes.project.remote ]]
```

#### `http_port`

HTTP port to use for Filebrowser. `"80"` by default.

#### Example

```
args:
	http_port: "8888"
```

#### `http_auth`

Whetther to use HTTP authentication for Filebrowser or not. `"True"` by default.

#### Example

```
args:
	http_auth: "False"
```

#### `job_name`

The name of the Filebrowser server. Use it to generate a predictable job hostname. `"filebrowser"` by default.

#### Example

```
args:
	job_name: "browser-job"
```

#### `preset`

Resource preset, used to run the server. The first one in the `apolo config show` list by default.

#### Example

```
args:
	job_name: "browser-job"
```

{% hint style="info" %}
Feel free to check the [Filebrowser action repository](https://github.com/apolo-actions/filebrowser).
{% endhint %}
