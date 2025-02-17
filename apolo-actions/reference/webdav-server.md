# WebDAV Server

With this action, you can serve your data in the Apolo cluster (storage folder, disk, etc.) using [rclone's WebDAV server](https://rclone.org/webdav/).

The only required parameter is the reference to the target remote volume.

#### Quick example

```
jobs:
  webdav_server:
    action: gh:apolo-actions/webdav_server@master
    args:
      volume_remote: ${{ volumes.project.remote }}
```

### Arguments

#### `volume_remote`

Reference to the target volume you want to serve.

#### Example

```
args:
	volume_remote: ${{ volumes.project.remote }}
```

#### `http_auth`

Whether to enable Apolo platform-based authentication or not. If this argument is disabled (set to `""` by default), your WebDAV will not be protected by the Apolo SSO (single sign-on authentication). It has no impact on the `rclone serve webdav` parameters.

#### Example

```
args:
	http_auth: "True"
```

#### `port`

Rclone WebDAV server port. Useful if you want to access the server within the cluster - for instance, from another job. `"8080"` by default.

#### Example

```
args:
	port: "8484"
```

#### `job_name`

The name of the WebDAV server. Use it to generate a predictable job hostname. `"webdav"` by default.

#### Example

```
args:
	job_name: "webdav-job"
```

#### `job_lifespan`

The amount of time for which the WebDAV server job container will be active. `"1d"` by default.

#### Example

```
args:
	job_lifespan: 1d4h20m30s
```

#### `preset`

The resource preset to use when running this job. `"cpu-small"` by default. You can view the list of available presets by running `apolo config show`.

#### Example

```
args:
	preset: "cpu-medium"
```

#### `extra_params`

Extra parameters for the `rclone serve webdav` command.

{% hint style="info" %}
Feel free to check the [WebDAV Server action repository](https://github.com/apolo-actions/webdav_server).
{% endhint %}
