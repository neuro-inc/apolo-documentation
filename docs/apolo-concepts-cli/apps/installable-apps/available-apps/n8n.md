# n8n

### Overview

n8n is a powerful workflow automation platform that simplifies data integration and process orchestration. It's an excellent tool for MLOps, data engineering, and general automation, allowing users to connect various applications, APIs, and services via a visual workflow editor.

This guide provides instructions for installing the n8n application on your Apolo project using the Apolo Command Line Interface (CLI). Refer to the [main n8n](../../../../apolo-console/apps/installable-apps/available-apps/n8n.md) application documentation page for more information about installing it using Apolo Console.

### Managing application via Apolo CLI

n8n can be installed on Apolo either via the CLI or the [Web Console](../../../../apolo-console/apps/installable-apps/available-apps/n8n.md). Below are the detailed instructions for installing n8n using Apolo CLI.

**Install via Apolo CLI**

**Step 1** — Navigate to the Apolo Web Console and go to the Apps page. Find n8n from the list and click the corresponding "Install" button.

**Step 2** — Fill in all the required configuration fields on the installation page (Resource Presets, Replicas, Persistent Storage, Database Configuration, etc.). Once configured, click the **Download** button located to the left of the "Install" button. This will download the fully configured YAML template file (e.g., `<app-template-file>.yaml`) needed for the CLI installation.\
\
You can also obtain a template file by using the following command:

```bash
apolo app-template get n8n > n8n.yaml
```

**Step 3** — Use the CLI command to install the application using the downloaded configuration file:

```bash
apolo app install -f <app-template-file>.yaml
```

Monitor the application status transitions via CLI:

```bash
apolo app list
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

**Step 4** — To uninstall the application via CLI, use:

```bash
apolo app uninstall <app-id>
```

### Configuration Parameters Explained (from the YAML template)

The downloaded YAML file will contain all the parameters configured in the Web Console. Below is an example of the structure you might download:

```yaml
display_name: "my-n8n-workflow"
template_name: n8n
template_version: n8n-app # Note: This version might change
input:
    main_app_config:
        replica_scaling:
            min_replicas: 1
            max_replicas: 3
            target_cpu_utilization_percentage: 80
            target_memory_utilization_percentage: null
        preset:
            name: cpu-medium
    worker_config:
        replicas: 1
        preset:
            name: cpu-medium
    webhook_config:
        replicas: 1
        preset:
            name: cpu-medium
    valkey_config:
        architecture:
            architecture_type: replication
            replica_preset: {name: cpu-medium}
            autoscaling: {min_replicas: 1, max_replicas: 2, target_cpu_utilization_percentage: 80, target_memory_utilization_percentage: null}
        preset:
            name: cpu-medium
    networking:
        ingress_http:
            auth: {type: apolo_auth}
    database_config:
        database:
            database_type: sqlite
    # Persistent Storage is highly recommended for retaining data:
    # persistent_storage:
    #     storage_path: 
    #         path: storage:apps/n8n-data

```

Key sections explained:

1. **`display_name`**: The name displayed for the installed application in Apolo Console.
2. **`template_name` / `template_version`**: Identifies the application template used.
3. **`input.main_app_config.preset.name`**: Specifies the resource preset for the primary n8n service (e.g., `cpu-medium`).
4. **`input.main_app_config.replica_scaling`**: Defines the scaling strategy for the main application (can be fixed or autoscaling).
5. **`input.worker_config` / `input.webhook_config`**: Configuration for dedicated worker and webhook services, including replicas and resource presets.
6. **`input.valkey_config`**: Configuration for the internal Valkey/Redis instance used for caching and queue management.
7. **`input.networking.ingress_http.auth`**: Ensures secure network access (default is `apolo_auth`).
8. **`input.database_config.database.database_type`**: Chooses between `sqlite` (local file storage) or `postgres`.

### References

* [Installing n8n using Apolo Web Console](../../../../apolo-console/apps/installable-apps/available-apps/n8n.md)
* [n8n documentation](https://docs.n8n.io/)
* [Apolo application management ](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
