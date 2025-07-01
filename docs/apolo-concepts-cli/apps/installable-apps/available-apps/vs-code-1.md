# Jupyter

### Overview

Jupyter Apolo is an application designed to facilitate interactive computing and data analysis. It supports both Jupyter Lab and Jupyter Notebook interfaces, providing a flexible and powerful environment for data scientists and analysts to develop and share their work. With seamless integration into Apolo's ecosystem, it leverages Apolo Storage for storage and MLFlow for experiment tracking, making it an ideal choice for collaborative and scalable data science workflows.

### Installing

Jupyter can be installed on Apolo either via the CLI or the [Web Console](../../../../apolo-console/apps/available-apps/jupyter-notebook.md#install-via-apolo-web-console). Below are the detailed instructions for installing Jupyter using Apolo CLI.

#### Install via Apolo CLI

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get jupyter -o myjupyter.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Example of myjupyter.yaml

template_name: jupyter
template_version: latest
display_name: myjupyter
input:
  preset:
    name: cpu-medium
  jupyter_specific:
    jupyter_type: lab
  extra_storage_mounts:
    mounts:
      - storage_uri:
          path: storage:additional-data
        mount_path:
          path: /mnt/data
        mode:
          mode: rw
  networking:
    http_auth: true
```

Explanation of configuration parameters:

1. **Jupyter Type**: Set to `lab` to deploy Jupyter Lab. Alternatively, set to `notebook` for Jupyter Notebook.
2. **Override Default Storage Mount**: Enabled to allow custom storage configurations.
3. **Extra Storage Mounts**: Enabled to attach additional storage volumes.
4. **HTTP Authentication**: Enabled for secure access.
5. **Resource Preset**: Set to `cpu-medium` to define the hardware resources allocated.

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f myjupyter.yaml
```

Monitor the application status using:

```bash
apolo app list
```

To uninstall the application, use:

```bash
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](vs-code-1.md#usage) section.

### References

* [Installing VSCode using Apolo Console](../../../../apolo-console/apps/installable-apps/available-apps/vs-code.md)
* [Code Server project](https://github.com/coder/code-server)
