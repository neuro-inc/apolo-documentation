# Valkey

## Overview

Valkey is a high-performance, open-source key-value datastore designed for ultra-fast data access. It is commonly used for caching, session storage, real-time analytics, and message queues.

Valkey is API-compatible with Redis OSS, making it easy to integrate into existing applications while benefiting from an actively evolving open-source ecosystem.

Valkey is suitable for a broad range of use cases, including:

* Caching frequently accessed data
* Managing user sessions
* Real-time data processing
* Lightweight message brokering

It is primarily designed for ephemeral or low-latency data access rather than long-term persistent storage.

## Architecture

Vakley architecture defines the deployment topology of your Valkey instance. It determines how data is stored, replicated, and served across nodes, directly impacting availability, performance, and operational complexity.

### Supported Modes

#### **1. Standalone**

A single-node Valkey deployment.

**Characteristics:**

* One instance handles all reads and writes
* No replication or redundancy
* Simplest configuration and lowest resource usage

**Best for:**

* Development and testing
* Ephemeral caching
* Non-critical workloads

#### **2. Replication**

A multi-node deployment with one primary node and one or more replicas.

**Characteristics:**

* Primary node handles writes
* Replicas asynchronously replicate data from the primary
* Replicas can serve read requests (depending on configuration)

**Best for:**

* Production environments
* High-read workloads
* Systems requiring resilience

#### Replication Details

* **Replication type:** Asynchronous (replicas may lag slightly behind primary)
* **Failover:**
  * Manual by default
  * Automatic failover requires additional components (e.g., Sentinel)
* **Read scaling:** Applications can be configured to read from replicas

***

## Accessing the Valkey App

1. Navigate to the **Apolo Console**
2. Open the **Apps** section from the left-hand navigation
3. Locate the **Valkey** application in the available apps list
4. Click **Install**

If the app is already installed, you can manage it from the **Installed Apps** tab.

### Installing the Valkey App

#### 1. Configure Resources

Select a resource preset based on your workload requirements:

* `cpu-small` — lightweight caching workloads
* `cpu-medium` — moderate traffic and session storage
* `cpu-large` — high-throughput, low-latency workloads

Valkey is memory-intensive, so ensure sufficient RAM allocation.

#### 2. Configure Storage

Depending on your use case, choose one of the following:

* **Ephemeral storage** – for cache-only workloads
* **Persistent storage** – for data that should survive restarts

#### 3. Configure Architecture

Set the `ValkeyArchitecture` parameter to define the deployment topology:

* **standalone** – a single-node deployment with no replication. Suitable for development, testing, and non-critical workloads.
* **replication** – a primary-replica deployment that provides redundancy, improved availability, and read scaling. Recommended for production use cases.

When using **replication**, you can configure the number of replicas to balance read performance and resource usage.

#### 4. Networking Settings

* Expose the Valkey port (default: `6379`)
* Configure internal access for other services within the cluster
* Optionally enable external access (not recommended unless secured)

#### 5. Security Configuration

* Enable authentication if external access is configured
* Use Apolo Secrets for credentials management
* Restrict access via internal networking whenever possible

#### 6. Metadata

Provide a name for your Valkey instance. If omitted, a system-generated name will be assigned.

#### 7. Install the App

Click **Install** to deploy the application.

Once deployed, the app will appear in the **Installed Apps** tab with its status and connection details.

### Managing Installed Valkey Instances

To manage your Valkey deployment:

1. Go to the **Installed Apps** tab
2. Select your Valkey instance
3. Open the **Details** view

You will find:

* Application metadata (name, ID, owner)
* Current status (e.g., progressing, healthy)
* Configuration inputs
* Logs
* Output values (connection endpoints)

### Connecting to Valkey

Valkey exposes connection details via application outputs.

Typical connection parameters:

* Host
* Port (`6379`)
* Password (if enabled)

#### Example connection (CLI)

```bash
valkey-cli -h <host> -p 6379
```

#### Example (Python)

```python
import redis

client = redis.Redis(
    host="<host>",
    port=6379,
    password="<password>",
)

client.set("key", "value")
print(client.get("key"))
```

***

## Usage

### Caching

Store frequently accessed data to reduce load on primary databases.

#### Session Storage

Maintain user session state for web applications.

#### Message Queues

Use lists or streams for lightweight queuing systems.

### Best Practices

* Use Valkey as a **cache layer**, not a primary database
* Enable persistence only when necessary
* Monitor memory usage closely
* Use replication or Sentinel setups for high availability
* Restrict access to internal cluster networking

### Scaling and High Availability

Valkey deployments can be scaled by:

* Increasing resource presets (vertical scaling)
* Adding replicas (read scaling)
* Using Sentinel for automatic failover (if supported)

### Cleanup

To remove the Valkey instance:

1. Navigate to the app **Details** page
2. Click **Uninstall**

***

## References

* [Valkey Documentation](https://valkey.io/docs/?utm_source=chatgpt.com)
* [Valkey GLIDE (official client libraries overview)](https://glide.valkey.io/overview/?utm_source=chatgpt.com)
* [Valkey GLIDE Python API](https://glide.valkey.io/languages/python/api/?utm_source=chatgpt.com)
* [Avaliable apps](./)
* [N8N](n8n.md) application integration
* [Dify](dify.md) application integration
