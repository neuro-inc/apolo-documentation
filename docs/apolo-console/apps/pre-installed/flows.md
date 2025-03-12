---
description: 'Apolo Flow: MLOps Pipeline Engine'
---

# Flows

**Apolo Flow** is a powerful pipeline engine designed for MLOps workflows on the Apolo platform. It enables seamless orchestration, automation, and execution of machine learning pipelines.

## Use Cases

* Automating ML workflows, from data ingestion to model deployment.
* Running batch and live workflows for continuous training and inference.
* Managing dependencies and execution order across pipeline steps.
* Standardizing and versioning workflows for reproducibility and collaboration.

## Example Use Case

Imagine a data science team working on a fraud detection model. They can use Apolo Flow to:

1. Ingest transaction data from multiple sources.
2. Preprocess the data and extract relevant features.
3. Train and validate multiple models in parallel.
4. Deploy the best-performing model into production.

## Models of Operation

* **CLI Usage**: Provides a command-line interface for managing pipelines, configuring workflows, and executing actions.
* **Configuration Files**: Uses structured configuration files to define workflow syntax, actions, and dependencies.
* **Workflow Syntax**: Supports batch (pipeline) and live (interactive) workflows, allowing users to define execution logic and contexts.

## Web Console Capabilities

Apolo Web console  includes a Flow section for monitoring and managing pipeline execution. Users can:

* List workloads running as part of flows, including live jobs and bakes (batch execution).
* Monitor and control the lifecycle of jobs, tasks, and entire pipelines.
* Retrieve pipeline statuses, view pipeline DAG with highlighted statuses, step-by-step execution details, inspect logs.
* Kill jobs, individual tasks, or full pipeline if necessary.
* Access detailed outputs for each pipeline step, enabling debugging and performance optimization.

For detailed documentation, refer to the dedicated Apolo Flow reference guide.

### References

* [Apolo Flow documentation](https://app.gitbook.com/o/-MMLX64i1AQdS3ehf2Kg/s/-MMLOF_FqiWBMcOdY8cj/)

