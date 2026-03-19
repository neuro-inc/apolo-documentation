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

#### 3. Networking Settings

* Expose the Valkey port (default: `6379`)
* Configure internal access for other services within the cluster
* Optionally enable external access (not recommended unless secured)

#### 4. Security Configuration

* Enable authentication if external access is configured
* Use Apolo Secrets for credentials management
* Restrict access via internal networking whenever possible

#### 5. Metadata

Provide a name for your Valkey instance. If omitted, a system-generated name will be assigned.

#### 6. Install the App

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

<br>
