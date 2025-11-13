# Apolo Jobs vs Apps

### Overview

The Apolo platform provides two distinct approaches for running workloads: **Jobs** and **Apps**. While both enable you to execute containerized workloads on the platform, they serve fundamentally different purposes and are designed for different use cases. Understanding when to use each is crucial for effectively leveraging the Apolo platform.

### Conceptual Differences

#### Jobs: Ad-Hoc Containerized Workloads

Jobs are the fundamental unit of compute execution in Apolo. They represent **ephemeral, containerized tasks** that you launch, monitor, and manage with full control over their configuration and lifecycle.

**Key Characteristics:**

* **Transient nature**: Jobs have a defined beginning and end
* **Fine-grained control**: Every parameter can be customized per execution
* **Imperative approach**: You explicitly specify what to run and how
* **Flexible lifecycle**: Can be interactive, detached, or batch-oriented
* **No pre-configuration required**: Launch directly with CLI commands

#### Apps: Pre-Packaged, Reusable Solutions

Apps are **higher-level abstractions** that package commonly-used tools and services into ready-to-deploy solutions. They come in two categories:

1. **Pre-installed Apps**: Essential tools that come standard with the Apolo console, providing instant access to key functionalities without setup
2. **Installable Apps**: Popular ML/AI tools and services that can be installed as needed

**Key Characteristics:**

* **Persistent services**: Designed for long-running or frequently-used tools
* **Pre-configured**: Come with sensible defaults and guided setup
* **Declarative approach**: You configure and install, the platform handles deployment
* **Standardized interface**: Consistent management across all apps
* **Reusability**: Install once, use repeatedly

### Technical Comparison

<table><thead><tr><th width="141.52734375">Aspect</th><th width="304.8203125">Jobs</th><th>Apps</th></tr></thead><tbody><tr><td><strong>Lifecycle</strong></td><td>Ephemeral, task-oriented</td><td>Persistent, service-oriented</td></tr><tr><td><strong>Configuration</strong></td><td>Fully customizable per execution</td><td>Template-based with parameters</td></tr><tr><td><strong>Management</strong></td><td>Direct CLI/Console control</td><td>App management interface</td></tr><tr><td><strong>Purpose</strong></td><td>Training, processing, experimentation</td><td>Development environments, services</td></tr><tr><td><strong>State</strong></td><td>Stateless by default</td><td>Often stateful</td></tr><tr><td><strong>Deployment</strong></td><td>Immediate execution</td><td>Install then access the app via URL</td></tr><tr><td><strong>Access Method</strong></td><td>CLI (<code>apolo job run</code>) or Console widget</td><td>CLI (<code>apolo app/apolo app-template</code>) or Console widget</td></tr></tbody></table>

### Use Cases and Examples

#### When to Use Jobs

Jobs are ideal for **computational tasks** that have a clear start and end point:

**1. Model Training**

Training a machine learning model is a perfect job use case:

```bash
apolo job run \
  --name training-run \
  --preset gpu-small \
  --volume storage:data:/workspace/data:rw \
  --volume storage:code:/workspace/code:rw \
  --env BATCH_SIZE=32 \
  --env LEARNING_RATE=0.001 \
  --workdir /workspace \
  --life-span 24h \
  --tag experiment \
  --description "Training run with custom parameters" \
  pytorch/pytorch:latest \
  -- python ./code/train.py --epochs 100
```

**Why a Job?**

* Has a defined duration (training epochs)
* Requires specific resource allocation for this run
* Needs custom environment variables per experiment
* Will terminate when complete

**2. Data Processing Pipelines**

Batch processing data is another classic job scenario:

```bash
apolo job run \
  --name data-preprocessing \
  --preset cpu-large \
  --volume storage:raw-data:/raw-data:ro \
  --volume storage:processed-data:/processed-data:rw \
  --volume storage:code:/code:rw \
  --life-span 6h \
  python:3.9 \
  -- python /code/preprocess.py --input /raw-data --output /processed-data
```

**Why a Job?**

* One-time or periodic execution
* Clear input and output
* Completes when processing is done

**3. Model Inference (Batch)**

Running inference on a dataset:

```bash
apolo job run \
  --name batch-inference \
  --preset gpu-small \
  --volume storage:models:/models:ro \
  --volume storage:code:/code:ro \
  --volume storage:test-data:/test-data:ro \
  --volume storage:predictions:/predictions:rw \
  tensorflow/tensorflow:latest-gpu \
  -- python /code/inference.py --model /models/best_model.h5
```

**Why a Job?**

* Batch processing mode
* Temporary execution
* No need for persistent service

**4. Debugging and Experimentation**

Interactive exploration and debugging:

```bash
apolo job run \
  --name debug-session \
  --preset gpu-small \
  --volume storage::/code:rw \
  --http-port 8888 \
  quay.io/jupyter/base-notebook:latest \
  -- jupyter lab --allow-root
```

**Why a Job?**

* Temporary debugging session
* Custom configuration for specific investigation
* Will be terminated when done

#### When to Use Apps

Apps are ideal for **tools and services** that you use repeatedly or that need to run continuously:

**1. Development Environments**

Apps like **Jupyter Notebook**, **JupyterLab**, **VS Code**, or **PyCharm** provide persistent development environments:

**Why an App?**

* Used regularly for multiple projects
* Requires consistent configuration
* Benefits from standardized setup
* May maintain session state
* Accessed through web interface

**Example Scenario**: You're developing multiple ML models over weeks. Instead of launching a new Jupyter job each time with custom configuration, install the Jupyter app once with your preferred settings, and access it whenever needed.

**2. Model Serving Services**

Apps like **vLLM** or **Text Embeddings Inference** provide persistent inference APIs:

**Why an App?**

* Must be continuously available
* Serves multiple requests over time
* Requires stable endpoint
* Scalable
* Benefits from managed deployment

**Example Scenario**: You need to serve a large language model to multiple applications. The vLLM app provides a persistent API endpoint with optimized inference, handling requests continuously rather than as batch jobs.

**3. MLOps Infrastructure**

Apps like **MLflow** provide persistent tracking and management services:

**Why an App?**

* Centralizes experiment tracking
* Maintains historical data
* Provides web interface
* Used by multiple team members

**Example Scenario**: Your team runs many training jobs and needs to track experiments, compare metrics, and manage model versions centrally. MLflow app provides a persistent service that all jobs can report to.

**4. Data management**

Apps like **PostgreSQL** provide persistent database services:

**Why an App?**

* Data persistence required
* Accessed by multiple jobs/services
* Benefits from managed configuration
* Needs consistent availability

**Example Scenario**: Multiple training jobs need to log results to a database, and various analysis scripts need to query that data. A PostgreSQL app provides a stable, always-available database service.

**5. AI Applications**

Apps like **Stable Diffusion**, **Fooocus**, or **Dify** provide ready-to-use AI services:

**Why an App?**

* Pre-configured for optimal performance
* Provides user interface
* Benefits from standardized deployment
* Used interactively over time

**Example Scenario**: You need to generate images regularly for different projects. The Stable Diffusion app provides a persistent web interface with the model already loaded and optimized, rather than launching a new job each time.

### Decision Framework

#### Choose **Jobs** when:

✅ You need **maximum flexibility** in configuration\
✅ The workload is **temporary or one-time**\
✅ You're **experimenting** with different parameters\
✅ The task has a **clear completion point**\
✅ You need **fine-grained control** over resources\
✅ You're running **batch processing**\
✅ Each execution needs **different settings**

#### Choose **Apps** when:

✅ You need a **persistent service**\
✅ You'll use the tool **repeatedly**\
✅ You want **standardized deployment**\
✅ The tool requires a **web interface**\
✅ Multiple users need **shared access**\
✅ You need **managed configuration**\
✅ The service should be **always available**

### Practical Examples: Same Tool, Different Approaches

#### Example 1: Jupyter Notebook

**As a Job** (Ad-hoc exploration):

```bash
apolo job run \
  --name quick-analysis \
  --preset cpu-small \
  --volume storage::/data:ro \
  --http-port 8888 \
  --life-span 4h \
  jupyter/scipy-notebook \
  -- jupyter lab --allow-root
```

**Use when**: You need a quick, temporary notebook session for specific analysis

**As an App** (Regular development):

* Install Jupyter app from Available Apps
* Configure persistent storage and preferred settings
* Access through Apps interface whenever needed

**Use when**: You regularly use Jupyter for development across multiple projects

#### Example 2: Model Inference

**As a Job** (Batch inference):

```bash
apolo job run \
  --name batch-predictions \
  --preset gpu-small \
  --volume storage:models:/models:ro \
  --volume storage:data:/data:ro \
  --volume storage:outputs:/output:rw \
  vllm/vllm-openai:latest \
  -- python batch_inference.py
```

**Use when**: You need to process a dataset once

**As an App** (Inference service):

* Install vLLM app
* Configure model and serving parameters
* Provides persistent API endpoint

**Use when**: Multiple applications need real-time inference via API

### Working with Both: A Complete Workflow

A typical ML workflow often uses **both Jobs and Apps together**:

1. **Apps for Infrastructure**:
   * Install MLflow app for experiment tracking
   * Install PostgreSQL app for results storage
   * Install Jupyter app for development
2. **Jobs for Computation**:
   * Run training jobs with different hyperparameters
   * Each job logs to MLflow app
   * Each job stores results in PostgreSQL app
   * Debug issues by launching temporary jobs
3. **Apps for Deployment**:
   * Once model is trained, deploy via vLLM app for serving
   * Use Service Deployment app for custom inference API

### Pre-installed vs Installable Apps

#### Pre-installed Apps

These are available immediately in every Apolo project, you can find an overview for such applications on a dedicated [#pre-installed-apps](../apolo-console/apps/#pre-installed-apps "mention") page.

#### Installable Apps

These must be configured and installed per project based on your needs. The list of installable applications grows constantly and can be found at [#pre-installed-apps](../apolo-console/apps/#pre-installed-apps "mention") page.

### CLI vs Console Management

#### Jobs Management

**CLI** (More control):

```bash
# Run a job
apolo job run pytorch/pytorch:latest -- python train.py

# List jobs
apolo job ls --status running

# View logs
apolo job logs my-job

# Debug interactively
apolo job exec my-job -- /bin/bash

# Stop a job
apolo job kill my-job
```

**Console** (Visual interface):

* Use Custom Job widget on dashboard
* Select preset, image, command
* Optional name and HTTP port
* Visual job status monitoring

#### Apps Management

**CLI**:

```bash
# List available apps
apolo app ls

# Install an app
apolo app install jupyter

# Dump logs from jupiter app instance
apolo app logs jupyter-instance
```

**Web Console** (Recommended):

* Navigate to Apps section
* Browse Available Apps
* Click Install with guided configuration
* Manage through Apps interface

**Apps are typically easier to manage through the Web Console** due to their configuration complexity since Apolo Web Console has a more intuitive flow, while **Jobs benefit from CLI flexibility** for scripting and automation.

### Best Practices

#### For Jobs

1. **Use meaningful names**: `--name training-resnet50-lr001` not `--name job1`
2. **Tag appropriately**: `--tag experiment --tag production`
3. **Set lifecycle limits**: `--life-span 24h` to prevent runaway costs
4. **Mount storage correctly**: Use `:ro` for inputs, `:rw` for outputs
5. **Leverage presets**: Use standardized resource configurations
6. **Monitor resource usage**: Use `apolo job top` to track consumption

#### For Apps

1. **Install what you need**: Don't clutter with unused apps
2. **Configure carefully**: Review all parameters during installation
3. **Use appropriate presets**: Match resources to app requirements
4. **Manage lifecycle**: Stop apps when not in use to save credits
5. **Organize by project**: Install apps in relevant projects
6. **Update documentation**: Note custom configurations for team use

### Advanced Patterns

#### Hybrid Workflows

Combine Jobs and Apps for sophisticated workflows:

```bash
# 1. Use MLflow app for tracking
# (Already installed and running)

# 2. Launch multiple training jobs
for lr in 0.001 0.01 0.1; do
  apolo job run \
    --name "train-lr-${lr}" \
    --env MLFLOW_TRACKING_URI="http://mlflow-app:5000" \
    --env LEARNING_RATE="${lr}" \
    pytorch/pytorch:latest \
    -- python train.py
done

# 3. Use Jupyter app to analyze results
# (Access through Apps interface)

# 4. Deploy best model via vLLM app
# (Configure with best model path)
```

### Conclusion

The choice between Jobs and Apps in Apolo comes down to **persistence** and **reusability**:

* **Jobs** are for **computational tasks**: training, processing, batch inference, experimentation
* **Apps** are for **services and tools**: development environments, inference APIs, databases, managed services

Think of Jobs as **running programs** and Apps as **installed applications**. Both are essential to an effective ML workflow, and understanding when to use each will help you leverage the Apolo platform most effectively.

Most users will find themselves using **Apps for their development environment and infrastructure**, while running **Jobs for their actual computational workloads**. This separation provides the right balance of convenience and control for modern ML operations.

## References

* [Apolo Apps overview](../apolo-console/apps/)
* [Apolo Apps management](../apolo-console/apps/installable-apps/managing-apps.md)
* [Apolo Jobs overview](../apolo-console/apps/pre-installed/jobs/)
* [Apolo Jobs management via CLI](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/job)
* [Apolo Jobs management via web console](../apolo-console/apps/pre-installed/jobs/)
