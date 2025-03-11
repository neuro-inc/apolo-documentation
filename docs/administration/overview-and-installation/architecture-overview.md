# Architecture Overview

Apolo is an MLOps PaaS that facilitates and supports the full cycle of ML operations such as data processing, model development, training, and deployment.

### High-level Architecture

Apolo consists of multiple logical parts:

* The **Control Plane**, dedicated to managing all aspects of the platform;
  * **The CLI**;
  * **The Web Application**;
* A **Compute Cluster**, a first-class citizen within the Control Plane, dedicated to running ML workloads;

### Control Plane

The Control Plane is the brains of the platform. Its responsibilities include, but are not limited to:

* Managing users and their resource quotas;
* Managing RBAC/ACL;
* Managing Compute Clusters and their lifecycle;
* Storing the metadata of jobs;
* Managing the lifecycle of jobs;
* Sending user notifications;
* Hosting the Web Application;
* Hosting the Web Documentation;

The Control Plane requires:

* A load balancer with an external IP address;
* Kubernetes for running a fleet of management microservices;
  * A properly configured default StorageClass;
  * Traefik as the Ingress Controller;
* PostgreSQL for storing job metadata, user metadata, and roles;
* Redis for storing the ACLs of roles and queueing user notifications;

#### Components

**Auth API**

A typical extensible API for managing and enforcing hierarchical access control lists (ACLs) that are bound to roles.

**Admin API**

A high-level semantic API on top of the Auth API for managing users, clusters they have access to, roles they take in these clusters, their quotas within the clusters, etc.

**Config API**

An API for managing the lifecycle of clusters. This API allows cluster provisioning, changing cluster configuration, cluster decommissioning, etc. It uses Argo Workflows and Terraform under the hood.

**Jobs API**

An API for storing the metadata of jobs, managing their lifecycle, and enforcing user quotas. This service connects to the Kubernetes APIs of the clusters.

**Ingress Auth**

A service that enforces the optional single sign-on authentication scheme for jobs that expose an HTTP port. This also implements the forward authentication strategy for the Traefik Ingress Controller.

**Notifications API**

An API for sending email/Slack notifications for events such as job status transitions, user quota depletion, etc.

**Apps API**

An API for running a curated set of applications from within the Apolo Console.\\

![](</docs/.gitbook/assets/neu.ro-architecture-overview (1) (1).png>)

**Apolo Console**

A Apolo Console for managing compute workloads, running applications, managing secrets, checking user quotas, etc.

**Apolo CLI**

The main tool for interacting with both the control plane and compute clusters. Enables users to manage clusters and switch between them, manage compute workloads, manage storage and container images, etc

**Apolo-Flow CLI**

An engine for running computational workflows based on Apolo CLI and SDK .

**Apolo-Extras CLI**

A collection of useful tools based on Apolo CLI and SDK, e.g., transfer of storage and container images between clusters.\\

**Web Documentation**

An up-to-date user documentation for the Apolo Console and CLIs with usage examples and other useful information.\\

![](</docs/.gitbook/assets/neu.ro-architecture-overview-2 (1).png>)

### Compute Cluster

A typical compute cluster requires:

* A load balancer with an external IP address;
* A Kubernetes cluster for running a fleet of controlling microservices as well as compute workloads;
* A container registry, either managed by a cloud provider or running within the Kubernetes cluster;
* A low-latency high-throughput network file storage accessible via NFS/SMB;
* A cost-efficient object storage for archival purposes;

#### Components

**Storage API**

An API for managing the contents of the network file storage.

**Blob Storage API**

An API for managing buckets and objects in the object storage.

**Registry API**

A Docker-Registry-API-compatible service for managing container images.

**Monitoring API**

An API for streaming real-time and historical logs and real-time telemetry of compute workloads, as well as allowing remote command execution and port forwarding.

**Secrets API**

An API for managing user secrets.

**Disk API**

An API for managing persistent block storage.

**Reports**

A service for retrieving historical telemetry of compute workloads with respect to user permissions.\\

![](<../../.gitbook/assets/neu.ro-architecture-overview-3 (1).png>)
