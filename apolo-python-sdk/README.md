# The Apolo SDK for Python&#x20;

The Apolo SDK for Python is a comprehensive library designed to facilitate interaction with the Apolo Platform API. It provides developers with tools to manage various platform resources programmatically, including jobs, storage, images, users, secrets, disks, service accounts, and buckets.

[Link to full Apolo SDK documentation](https://apolo-sdk.readthedocs.io/latest/index.html)

### **Key Features:**

* **Jobs Management**: Submit, monitor, and control jobs within the Apolo Platform.
* **Storage Operations**: Handle file uploads, downloads, and manage storage resources.
* **Image Handling**: Manage container images, including pulling and pushing images to the Apolo registry.
* **User and Access Control**: Manage user information and permissions.
* **Secrets Management**: Securely store and retrieve sensitive information.
* **Disk Management**: Create and manage persistent storage disks.
* **Service Accounts and Buckets**: Manage service accounts and cloud storage buckets.

### **Installation:**

The SDK can be installed via pip:

`pip install -U apolo-sdk`

### **Getting Started:**

To begin using the SDK, authenticate with the Apolo Platform using the CLI:

`apolo login`

After authentication, initialize the client in your Python code:

```python
import apolo_sdk
async with apolo_sdk.get() as client:
async with client.jobs.list() as job_iter:
jobs = [job async for job in job_iter]
```

This example demonstrates how to instantiate a client and retrieve a list of jobs.

### **Documentation Structure:**

The documentation is organized into the following sections:

* **Usage**: Provides practical examples and guides on using the SDK for various tasks.
* **Reference**: Offers detailed API references for all classes, functions, and modules within the SDK.

For more information, visit the [Apolo SDK documentation](https://apolo-sdk.readthedocs.io/latest/index.html).
