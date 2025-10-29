# Apache Spark

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

As with other applications on Apolo, you can deploy Spark applications via web console, or using [Apolo CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/apache-spark.md).

#### Install via Apolo web console

Below is a brief description of how to deploy the Spark application with exactly the same configuration via Apolo web console. To find more details about how to manage your installable applications, refer to [Managing Apps](../managing-apps.md).

**Step 1** — find Spark Application in list of apps, and click "install" button.

**Step 2** — fill in the parameters for this application, they are divided in a few sections, as described below:

_**Application Configuration**_

* Application Type - this can be Python, Java, Scala or R
* Specify the main application file path - this should point to a file containing the main application logic stored in Apolo Files. For example: `storage:my-spark-job/main.py`
* Optionally add arguments. Example: `--input-path`, `storage:datasets/input.csv`, `--output-path`, `storage:results/`

_**Dependencies**_

* Optionally add PyPI packages, for example: `pandas==2.0.3`, `numpy>=1.24.0` - Apolo takes care of installing these dependencies for your
* Optionally add Maven packages, for example: `org.apache.spark:spark-sql_2.12:3.5.3`

_**Mounted Volumes**_

You can mount extra volumes into your application so that it is able to load data from or save results to Apolo Files. Examples:

* Input volume: `storage:datasets/` → `/data/input` (read-only)
* Output volume: `storage:results/` → `/data/output` (read-write)

_**Auto Scaling Configuration**_

* Initial Executors: 2
* Min Executors: 1
* Max Executors: 10
* Shuffle Tracking Timeout: 60

_**Driver Configuration**_

* Preset - select the preset type used by the driver node. Example: `cpu-medium`

_**Executor Configuration**_

* Instances: 3
* Preset `cpu-large` - select the preset type used by the executor node. Example: `cpu-medium`

After setting up all input parameters, click "install" to start the installation. You will be redirected to an application details page, which displays application inputs, outputs and health status.

Your Spark application is ready. The application outputs, including job status and results location are displayed on this screen. You could utilize these outputs with other applications or other workloads.

You could remove this application just like all other apps by clicking the "Uninstall" button in the upper right corner of the application details page.

### Usage

After installing your Spark application, it will automatically begin execution based on your configuration. The application runs as a managed service within the Apolo cluster, handling resource allocation, scaling, and job lifecycle management.

## References

* [Apolo applications management](../managing-apps.md)
* [Apache Spark application repository](https://github.com/neuro-inc/app-spark-job)
* [Apache pySpark documentation](https://spark.apache.org/docs/latest/api/python/index.html)
* [Apache Spark application management via CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/apache-spark.md)
