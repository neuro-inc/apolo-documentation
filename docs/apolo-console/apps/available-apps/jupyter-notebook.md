# Jupyter

### Overview

Jupyter Apolo is an application designed to facilitate interactive computing and data analysis. It supports both Jupyter Lab and Jupyter Notebook interfaces, providing a flexible and powerful environment for data scientists and analysts to develop and share their work. With seamless integration into Apolo's ecosystem, it leverages Apolo Storage for storage and MLFlow for experiment tracking, making it an ideal choice for collaborative and scalable data science workflows.

### Key Features

* **Resource Presets**: Choose from predefined resource configurations to optimize performance based on your workload needs.
* **Jupyter Type Selection**: Flexibility to deploy either Jupyter Lab or Jupyter Notebook, catering to different user preferences and project requirements.
* **Storage Integration**: Direct integration with Apolo Storage allows users to mount directories using the `storage:` prefix, facilitating easy data access and management.
* **HTTP Authentication**: Optional HTTP authentication ensures secure access to your Jupyter environment.
* **MLFlow Integration**: Built-in support for MLFlow enables seamless experiment tracking and model management, enhancing reproducibility and collaboration.
* **Extra Storage Mounts**: Attach additional storage volumes to your Jupyter application for expanded data capacity.
* **Kubernetes-Native**: Optimized for deployment within Kubernetes clusters, ensuring scalability and resilience.

### Installing

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

**Step 4** — To remove the application, use:

```bash
apolo app remove <app-id>
```

#### Install via Apolo Web Console

**Step 1** — Navigate to the Apps section and select Jupyter from the list.

**Step 2** — Configure the application:

* **Resource Preset**: Choose from the dropdown menu.
* **Jupyter App Configuration**: Select Jupyter Type, enable/disable storage options, and authentication settings.
* **Metadata**: Enter the application display name.

**Step 3** — Click "Install" to initiate deployment. You will be redirected to the application details page where you can monitor the installation progress and view application outputs, including access credentials.

### Usage

After installation, you can utilize Jupyter for various data science tasks and you can access it directly from your browser. Use the external web app URL provided in the application outputs to open Jupyter.

#### Example Workflow

1. **Start a Jupyter Session**: Access Jupyter Lab or Notebook through the provided URL.
2. **Data Analysis**: Load datasets from Apolo Storage and perform data analysis using Python or R.
3. **Experiment Tracking**: Utilize MLFlow integration to track experiments and manage model versions.
4. **Collaboration**: Share notebooks and results with team members within the Apolo platform.
