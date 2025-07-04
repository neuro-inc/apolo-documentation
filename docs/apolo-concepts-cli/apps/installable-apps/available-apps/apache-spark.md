# Apache Spark

### Overview

Apache Spark is a unified analytics engine for large-scale data processing that provides high-level APIs in Java, Scala, Python, and R. It offers built-in modules for streaming, SQL, machine learning, and graph processing, making it ideal for both batch and real-time data analytics workloads. Spark's in-memory computing capabilities and optimized execution engine deliver exceptional performance for iterative algorithms and interactive data analysis.

Apolo provides a managed Spark service that enables you to run scalable Apache Spark applications with configurable drivers, executors, and auto-scaling capabilities. The platform handles cluster management, resource allocation, and job orchestration, allowing you to focus on your data processing logic while ensuring optimal performance and cost efficiency in cloud-native environments.

Spark on Apolo comes with integrated support for dynamic scaling, dependency management, and seamless integration with Apolo Files for data storage and retrieval.

## Installing

#### Install via Apolo CLI

To find more details about how to manage your installable applications using Apolo CLI, refer to [Managing Apps](../managing-apps.md).

**Step 1** — use CLI command to get application configuration file template:

```ini
apolo app-template get spark -o myspark.yaml 
```

**Step 2** — fill in application parameters. Here is an example config file with some of those parameters:

```yaml
# Example of myspark.yaml

template_name: spark
template_version: v1.0.0
display_name: myspark
input:
  spark_application_config:
    type: Python
    main_application_file:
      path: storage:my-spark-job/main.py
    arguments:
      - "--input-path"
      - "storage:datasets/input.csv"
      - "--output-path"
      - "storage:results/"
    dependencies:
      pypi_packages:
        - "pandas==2.0.3"
        - "numpy>=1.24.0"
      packages:
        - "org.apache.spark:spark-sql_2.12:3.5.3"
    volumes:
      - storage_uri:
          path: storage:datasets/
        mount_path:
          path: /data/input
        mode:
          mode: r
      - storage_uri:
          path: storage:results/
        mount_path:
          path: /data/output
        mode:
          mode: rw
  spark_auto_scaling_config:
    initial_executors: 2
    min_executors: 1
    max_executors: 10
    shuffle_tracking_timeout: 60
  driver_config:
    preset:
      name: cpu-medium
  executor_config:
    instances: 3
    preset:
      name: cpu-large
  image:
    repository: spark
    tag: 3.5.3
```

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f myspark.yaml
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
