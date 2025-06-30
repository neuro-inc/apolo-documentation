# Spark Application

### Overview

Apache Spark is a unified analytics engine for large-scale data processing that provides high-level APIs in Java, Scala, Python, and R. It offers built-in modules for streaming, SQL, machine learning, and graph processing, making it ideal for both batch and real-time data analytics workloads. Spark's in-memory computing capabilities and optimized execution engine deliver exceptional performance for iterative algorithms and interactive data analysis.

Apolo provides a managed Spark service that enables you to run scalable Apache Spark applications with configurable drivers, executors, and auto-scaling capabilities. The platform handles cluster management, resource allocation, and job orchestration, allowing you to focus on your data processing logic while ensuring optimal performance and cost efficiency in cloud-native environments.

Spark on Apolo comes with integrated support for dynamic scaling, dependency management, and seamless integration with Apolo Files for data storage and retrieval.

### Key Features

**Multi-Language Support**: Native support for Python, Scala, Java, and R applications with optimized execution engines for each language.

**Dynamic Auto-Scaling**: Intelligent executor scaling based on workload demand, with configurable minimum and maximum executor limits to optimize resource utilization.

**Comprehensive Dependency Management**: Support for JAR files, Python packages, Maven packages, and custom archives with automatic distribution across cluster nodes. Apolo will install the required Pypi Packages in the nodes without the need to package and inject dependencies into them.

**Flexible Resource Configuration**: Configurable driver and executor presets with fine-grained control over CPU, memory, and instance allocation.

**Integrated Storage**: Seamless integration with Apolo Files for mounting data volumes and accessing datasets across your Spark applications.

**Container-Native**: Kubernetes-optimized deployment with custom container image support and enterprise-grade security.

### Installing

As with other applications on Apolo, you can deploy Spark applications via web console, or using Apolo CLI.

#### Install via Apolo web console

Below is a brief description of how to deploy the Spark application with exactly the same configuration via Apolo web console.

**Step 1** — find Spark Application in list of apps, and click "install" button.

**Step 2** — fill in the same parameters for this application

_Application Configuration_

* Select **Python** as the application type
* Specify the main application file path: `storage:my-spark-job/main.py`
* Add arguments: `--input-path`, `storage:datasets/input.csv`, `--output-path`, `storage:results/`

_Dependencies_

* Add PyPI packages: `pandas==2.0.3`, `numpy>=1.24.0`&#x20;
* Add Maven packages: `org.apache.spark:spark-sql_2.12:3.5.3`

_Mounted Volumes_

* Input volume: `storage:datasets/` → `/data/input` (read-only)
* Output volume: `storage:results/` → `/data/output` (read-write)

_Auto Scaling Configuration_

* Initial Executors: 2
* Min Executors: 1
* Max Executors: 10
* Shuffle Tracking Timeout: 60

_Driver Configuration_

* Select `cpu-medium` preset

_Executor Configuration_

* Instances: 3
* Select `cpu-large` preset

After setting up all input parameters, click "install" to start the installation. You will be redirected to an application details page, which displays application inputs, outputs and health status.

Your Spark application is ready. The application outputs, including job status and results location are displayed on this screen. You could utilize these outputs with other applications or other workloads.

You could remove this application just like all other apps by clicking the "Uninstall" button in the upper right corner of the application details page.

#### Install via Apolo CLI

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

### Usage

After installing your Spark application, it will automatically begin execution based on your configuration. The application runs as a managed service within the Apolo cluster, handling resource allocation, scaling, and job lifecycle management.
