# Files

## Overview

The Files application is a comprehensive file management system designed to help you organize and manage your network storage within the cluster. This documentation will guide you through its features and functionality using Apolo CLI. To learn more about the Files app and how you can use it in Apolo Console, visit the main [Files app](../../apolo-console/apps/pre-installed/files.md) page.

## **Command Line Interface (CLI)**

In addition to the graphical interface, you can manage your storage through our powerful command-line interface (CLI). The CLI provides advanced capabilities for file operations, particularly useful for automation and bulk operations.

### Managing Storage

Through the command line, you can perform all essential file operations using the `apolo storage` command set. Here are some key capabilities:

The CLI supports essential operations such as copying files (`cp`), creating directories (`mkdir`), moving files (`mv`), and removing files (`rm`). It also provides advanced features like storage usage analysis (`df`) and tree-style directory visualization (`tree`). When working with files programmatically or handling batch operations, the CLI offers precise control and automation capabilities.

For example, you can copy files to your storage using:

```bash
apolo storage cp local_file.txt storage:
```

Or list your storage contents in a tree format:

```bash
apolo storage tree
```

The CLI is particularly valuable for:

* Automating file operations in scripts
* Performing bulk file transfers
* Integration with development workflows
* Remote storage management
* Pattern-based file operations using glob patterns

For comprehensive documentation of all CLI commands and their options, please refer to our detailed [CLI documentation for storage](https://docs.apolo.us/index/apolo-cli/commands/storage).

### **Mounting Storage in Jobs**

When running computational jobs, you often need to access files from your storage. The Files system integrates seamlessly with our job execution system, allowing you to mount directories from your storage as volumes inside your containers. This gives your jobs direct access to your files, making it easy to process data and save results.

**How Storage Mounting Works**

When you mount a storage volume, you create a connection between a directory in your storage and a location inside your job's container. Think of it like creating a window between your storage and your running job - any files in the mounted storage directory become accessible from within your job.

You can mount volumes in two modes:

1. Read-write mode (`rw`): Allows your job to both read existing files and write new ones
2. Read-only mode (`ro`): Provides access to read files but prevents modifications

**Mounting Volumes Using the CLI**

To mount a volume when running a job, use the `--volume` (or `-v`) option with the `apolo job run` command (see [Apolo CLI reference ](https://docs.apolo.us/index/apolo-cli/commands/job#run)for more information on running jobs). The volume specification follows this format:

```bash
--volume=storage:<source-path>:<container-path>:<mode>
```

Where:

* `<source-path>`: The path in your storage (relative to your project root)
* `<container-path>`: Where the files will appear inside the container
* `<mode>`: Either `ro` (read-only) or `rw` (read-write)

For example, to mount your project's data directory in read-only mode:

```bash
apolo job run --volume=storage:/data:/workspace/data:ro python:3.9
```

You can mount multiple volumes in the same job:

```bash
apolo job run \
    --volume=storage:/input:/workspace/input:ro \
    --volume=storage:/output:/workspace/output:rw \
    pytorch/pytorch:latest
```

### **Using Storage in Workflows**

Workflows provide powerful capabilities for integrating your storage with computational jobs. Let's explore how to effectively use storage volumes in your workflow definitions.

**Understanding Volumes in Workflows**

Volumes in workflows create a three-way connection between:

1. Your local development environment
2. Your storage in the Apolo platform
3. The running jobs in your workflows

This three-way connection enables seamless data flow between development, storage, and computation. Let's look at how to define and use volumes in your workflow configuration.

**Defining Volumes**

In your workflow YAML file, you can define volumes in two ways:

1. **Direct Reference** - Specify the volume directly where it's needed:

```yaml
jobs:
  train_model:
    image: pytorch/pytorch:latest
    volumes:
      - storage:/data:/workspace/data:ro  # Direct reference
```

2. **Volume Definitions** - Define volumes centrally and reference them throughout:

```yaml
volumes:
  training_data:
    remote: storage:/data
    mount: /workspace/data
    local: ./data
    read_only: true

jobs:
  train_model:
    image: pytorch/pytorch:latest
    volumes:
      - ${{ volumes.training_data.ref }}
```

The second approach offers several advantages:

* Centralized volume management
* Reusability across multiple jobs
* Easier synchronization between local and remote storage

**Common Volume Patterns**

Here are some effective patterns for organizing your workflows with volumes:

1. **Input/Output Separation**:

```yaml
volumes:
  input_data:
    remote: storage:/data/input
    mount: /workspace/input
    read_only: true
  
  output_data:
    remote: storage:/data/output
    mount: /workspace/output

jobs:
  process_data:
    image: python:3.9
    volumes:
      - ${{ volumes.input_data.ref }}
      - ${{ volumes.output_data.ref }}
```

2. **Development Environment**:

```yaml
volumes:
  code:
    remote: storage:/src
    mount: /workspace/src
    local: ./src
  
  config:
    remote: storage:/config
    mount: /workspace/config
    local: ./config

jobs:
  develop:
    image: python:3.9
    volumes:
      - ${{ volumes.code.ref }}
      - ${{ volumes.config.ref }}
```

**Volume Synchronization**

When you define a volume with a `local` path, you can synchronize it with your storage using the CLI:

```bash
# Upload local changes to storage
apolo-flow upload

# Download storage changes locally
apolo-flow download
```

This is particularly useful for development workflows where you need to:

* Push code changes to a development environment
* Download computation results
* Share data between team members

**Best Practices**

1. **Use Read-Only Volumes for Input**: Protect your source data by mounting input volumes as read-only:

```yaml
volumes:
  dataset:
    remote: storage:/datasets/imagenet
    mount: /data
    read_only: true
```

2. **Organize Volumes by Purpose**: Structure your volumes based on their role:

```yaml
volumes:
  # Development volumes
  source_code:
    remote: storage:/src
    mount: /app/src
    local: ./src

  # Data volumes
  training_data:
    remote: storage:/data/train
    mount: /data/train
    
  # Output volumes
  model_artifacts:
    remote: storage:/models
    mount: /artifacts
```

3. **Use Volume References**: When the same volume is used in multiple jobs, define it once and reference it:

```yaml
jobs:
  train:
    volumes:
      - ${{ volumes.training_data.ref }}
  
  evaluate:
    volumes:
      - ${{ volumes.training_data.ref }}
```

For complete details on volume configuration options and advanced usage, refer to our [workflow syntax documentation](https://docs.apolo.us/index/apolo-flow-reference/workflow-syntax/live-workflow-syntax) and [Apolo Flow CLI reference](https://docs.apolo.us/index/apolo-flow-reference/cli).

### **Managing Data Transfers with apolo-extras**

The `apolo-extras` CLI provides powerful tools for managing data transfers between your storage systems. This extension to the main Apolo CLI enables seamless movement of data between external storage systems and your Apolo cluster, as well as transfers between different clusters.

For complete details on Apolo Extras CLI refer to the [CLI reference](https://app.gitbook.com/s/EicNFI9vPOX1TTMYRKT9/cli).

**Copying Data with apolo-extras data cp**

The `apolo-extras data cp` command serves as a bridge between external storage systems and your Apolo cluster. It supports multiple major cloud storage providers and protocols:

* Amazon Web Services (AWS) S3
* Google Cloud Storage (GCS)
* Microsoft Azure Blob Storage
* HTTP/HTTPS endpoints

Let's explore how to use this command effectively:

**Basic Data Copy Operations**

To copy data between storage systems, use this basic syntax:

```bash
apolo-extras data cp SOURCE DESTINATION
```

For example, to download data from S3 to your cluster storage:

```bash
apolo-extras data cp s3://my-bucket/dataset.zip storage:/data/
```

Or to upload data to Google Cloud Storage:

```bash
apolo-extras data cp storage:/results/experiment1 gs://my-bucket/results/
```

**Working with Archives**

The tool provides built-in compression and extraction capabilities. This is particularly useful when dealing with large datasets:

For extraction:

```bash
# Extract a downloaded archive directly into storage
apolo-extras data cp -x s3://datasets/images.tar.gz storage:/data/images/
```

For compression:

```bash
# Compress data before uploading
apolo-extras data cp -c storage:/results/experiment1 s3://results/exp1.tar.gz
```

The system automatically supports various archive formats:

* .tar.gz, .tgz (Gzipped tar archives)
* .tar.bz2, .tbz (Bzip2 compressed tar archives)
* .tar (Uncompressed tar archives)
* .gz (Gzip compressed files)
* .zip (ZIP archives)

**Resource Management**

You can control the resources allocated for data transfer operations:

```bash
# Use a specific compute preset
apolo-extras data cp -s gpu-small s3://large-dataset.zip storage:/data/

# Set a custom lifespan for long transfers
apolo-extras data cp -l 7200 storage:/huge-dataset gs://backup/
```

**Transferring Between Clusters**

The `apolo-extras data transfer` command is specifically designed for moving data between different Apolo clusters. This is particularly useful for:

* Migrating datasets between regions
* Sharing data between development and production environments
* Creating backups across clusters

Basic usage:

```bash
apolo-extras data transfer storage:/sourcedata storage://{other-cluster}/destination/
```
