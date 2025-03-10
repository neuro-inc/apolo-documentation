---
description: Running jobs using the Apolo CLI
---

# Jobs

## Overview

The **Jobs** App is a tool that allows users to schedule and execute containerized tasks and processes. It provides a user-friendly interface to manage these workloads, simplifying tasks like data processing, model training and inference, and other batch jobs. This app is designed to provide flexibility and control over how these jobs are run, while offering monitoring features for insights and debugging. For more information about the Jobs app, as well as detailed instruction of how to use it in Apolo Console, visit the main [Jobs app](../../apolo-console/apps/pre-installed/jobs/) page.

### **Running Jobs Using the CLI**

The `apolo job run` command lets you execute containerized workloads with precise control over their configuration. Let's explore how to create and configure jobs effectively using the CLI (see [Apolo CLI reference](https://docs.apolo.us/index/apolo-cli/commands/job#run) for more information on running jobs).

**Basic Job Execution** At its most basic, you only need to specify a Docker image to run a job:

```bash
apolo job run pytorch/pytorch:latest
```

This command runs the image with default settings. However, you'll often want to customize how your job runs.

**Essential Job Configuration**

Let's look at the core parameters you can configure when running a job:

**Container Configuration**

```bash
# Specify the command to run in the container
apolo job run pytorch/pytorch:latest -- python train.py --epochs 100

# Set a custom entrypoint
apolo job run --entrypoint=/custom_script.sh pytorch/pytorch:latest

# Set the working directory inside the container
apolo job run --workdir /workspace pytorch/pytorch:latest
```

**Environment and Data** You can pass data and configuration to your jobs in several ways:

```bash
# Set individual environment variables
apolo job run --env BATCH_SIZE=32 --env LEARNING_RATE=0.001 pytorch/pytorch:latest

# Load environment variables from a file
apolo job run --env-file config.env pytorch/pytorch:latest

# Mount storage volumes
apolo job run --volume storage::/data:rw --volume storage:/public:/public:ro pytorch/pytorch:latest
```

The volume syntax follows the pattern `storage:source:destination:mode`, where mode can be `rw` (read-write) or `ro` (read-only).

**Resource Allocation** Control the computing resources available to your job:

```bash
# Select a predefined resource configuration
apolo job run --preset gpu-small pytorch/pytorch:latest

# Request extended shared memory
apolo job run --extshm pytorch/pytorch:latest
```

**Job Identity and Organization** Give your job meaningful identifiers for better management:

```bash
# Assign a name to your job
apolo job run --name training-experiment-v1 pytorch/pytorch:latest

# Add a description
apolo job run --description "Training run with modified hyperparameters" pytorch/pytorch:latest

# Add tags for organization
apolo job run --tag experiment --tag v1 pytorch/pytorch:latest
```

**Runtime Behavior** Configure how your job behaves during execution:

```bash
# Set a maximum runtime for the job
apolo job run --life-span 12h pytorch/pytorch:latest

# Configure automatic restart behavior
apolo job run --restart on-failure pytorch/pytorch:latest

# Set job priority
apolo job run --priority high pytorch/pytorch:latest
```

**Network and HTTP Configuration** If your job serves HTTP content or needs port forwarding:

```bash
# Enable HTTP port forwarding (default port 80)
apolo job run --http-port 8080 pytorch/pytorch:latest

# Forward specific ports to your local machine
apolo job run --port-forward 8888:8888 pytorch/pytorch:latest

# Control HTTP authentication
apolo job run --no-http-auth pytorch/pytorch:latest
```

**Interactive Jobs** For jobs requiring interaction:

```bash
# Allocate a TTY for interactive use
apolo job run --tty pytorch/pytorch:latest

# Run without attaching to the job
apolo job run --detach pytorch/pytorch:latest
```

**Project and Organization Context** Specify where your job should run:

```bash
# Run in a specific project
apolo job run --project ml-experiments pytorch/pytorch:latest

# Run in a specific organization
apolo job run --org research-team pytorch/pytorch:latest

# Run on a specific cluster
apolo job run --cluster gpu-cluster pytorch/pytorch:latest
```

You can combine these options to create precisely configured jobs. Here's an example that brings together several common options:

```bash
apolo job run \
    --name training-run \
    --preset gpu-small \
    --volume storage::/data:rw \
    --env BATCH_SIZE=32 \
    --env LEARNING_RATE=0.001 \
    --workdir /workspace \
    --life-span 24h \
    --tag experiment \
    --description "Training run with custom parameters" \
    pytorch/pytorch:latest \
    -- python train.py --epochs 100
```

This comprehensive command creates a named job with custom resource allocation, mounted storage, environment variables, a working directory, runtime limit, and organizational tags, then executes a specific Python script with custom parameters.

Remember that after starting a job, you can monitor its progress using commands like `apolo job logs` to view output and `apolo job status` to check its current state.

I'll create a comprehensive section about debugging jobs that builds on the previous content and provides clear, practical explanations.

## **Debugging Jobs**

When developing and running containerized workloads, you'll often need to troubleshoot issues, monitor performance, or inspect the internal state of your jobs. Apolo provides several powerful tools to help you debug and understand your running jobs effectively.

### **Investigating Job Status and Logs**

The first step in debugging is understanding what's happening with your job. Apolo provides several ways to inspect your job's status and output:

```bash
# View detailed status of a specific job
apolo job status my-training-job

# Stream real-time logs from your job
apolo job logs my-training-job

# See logs with timestamps for correlation
apolo job logs --timestamps my-training-job

# View logs from a specific time period
apolo job logs --since 1h my-training-job
```

When you have many jobs running, you can filter the job list to find the ones you're interested in:

```bash
# Find jobs by status
apolo job ls --status failed --status running

# Search by name
apolo job ls --name training-experiment

# Filter by creation time
apolo job ls --since 2h --recent-first
```

### **Interactive Debugging**

Sometimes you need to interact directly with a running job. Apolo provides two powerful commands for this purpose:

```bash
# Start a new shell in the running container
apolo job exec my-training-job -- /bin/bash

# Execute a specific command
apolo job exec my-training-job -- ps aux
```

### **Port Forwarding for Web-Based Debugging**

Many debugging tools provide web interfaces. You can access these using Apolo's port forwarding capabilities:

```bash
# Forward multiple ports for different debugging tools
apolo job port-forward my-training-job \
    8080:8080 \  # For your application
    6006:6006 \  # For TensorBoard
    8888:8888    # For Jupyter notebooks

# Forward ports with HTTP authentication
apolo job run --http-port 8080 --http-auth my-image:latest
```

### **Attaching to Running Jobs**

The `apolo job attach` command creates an interactive connection to a running job, allowing you to observe its output and interact with it in real-time. This is particularly useful when you need to monitor progress, investigate issues, or work with interactive applications.

Basic attachment is straightforward:

```bash
apolo job attach my-training-job
```

This connects your terminal's input and output streams to the running job, letting you see any output and interact with the job directly. You can detach from the session without stopping the job by pressing Ctrl+P followed by Ctrl+Q.

A powerful feature of attach is its ability to combine terminal access with port forwarding. This is especially valuable when debugging applications that expose network services, like web servers or Jupyter notebooks:

```bash
# Attach and forward ports for both console access and web interfaces
apolo job attach my-debug-job \
    --port-forward 8888:8888 \  # For Jupyter
    --port-forward 6006:6006    # For TensorBoard
```

This command gives you both console access to your job and the ability to reach any network services through your local ports. For instance, with the above command, you could monitor training output in the console while accessing Jupyter at localhost:8888 and TensorBoard at localhost:6006 in your browser.

When debugging with attach, you can run additional Apolo commands in separate terminals to get a complete picture of your job's behavior. For example, you might run `apolo job top` in another terminal to monitor resource usage while interacting with your attached session.

Remember that closing your attach session doesn't terminate the job – it continues running in the background. You can always reattach to check on its progress or continue debugging as needed.

### **Preserving Job State**

After debugging and fixing issues, you might want to save the state of your container for future use or analysis. The `apolo job save` command creates a new image from your job's current state:

```bash
# Save the current state as a new image
apolo job save my-training-job image:debug-checkpoint-v1

# You can then use this image to start a new job
apolo job run image:debug-checkpoint-v1
```

This is particularly useful when:

* You've made changes to the container during debugging
* You want to capture the exact state where an issue occurred
* You need to share a reproducible environment with colleagues

### Monitoring resource usage

Use the following commands to display GPU, CPU and memory usage in a job.

```bash
# Watch resource utilization in real-time
apolo job top my-training-job

# Sort jobs by CPU or memory usage
apolo job top --sort cpu
```
