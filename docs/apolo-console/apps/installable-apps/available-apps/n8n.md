# n8n

#### Overview

n8n is a powerful workflow automation platform designed to simplify complex data integration tasks. It allows users to connect various applications, APIs, and services to automate data flows, build custom integrations, and streamline repetitive processes without extensive coding. This makes n8n an essential tool for MLOps, data engineering, and general business automation, enabling efficient handling of data pipelines and model deployment orchestration.

The platform supports a wide range of nodes for interacting with different services, from databases and cloud providers to AI/ML tools, making it highly versatile for creating custom MLOps workflows. Refer to the [Apolo CLI n8n](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/n8n.md) application documentation page for more information about installing it using Apolo CLI.

#### Key Features

* **Visual Workflow Editor:** Drag-and-drop interface for building complex workflows visually, making automation accessible to users with minimal coding background.
* **Extensive Integration Library:** Supports hundreds of integrations (nodes) for popular applications, databases, and APIs, including specific nodes for ML-related tasks.
* **Flexible Execution:** Offers various modes for triggering workflows, including webhooks, scheduled jobs, and manual execution.
* **Persistent Storage Options:** Supports both local SQLite and external PostgreSQL databases for persistent storage of workflow data, credentials, and execution history.
* **Scalable Architecture:** Can be configured to run in different modes (standalone or replication) and with separate worker and webhook configurations for handling high-volume traffic and distributed job processing.
* **Authentication and Security:** Integrates with Apolo Platform Authentication for secure network access and HTTP ingress management.

#### Installing

n8n can be installed on Apolo either via the [CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/n8n.md) or the Web Console. Below are the detailed instructions for installing n8n using Apolo Console.

**Install via Apolo Web Console**

**Step 1** — Navigate to the Apps page, find n8n from the list and click the corresponding "Install" button. This will redirect you to the installation page.

**Step 2** — Configure the application by filling the required fields and click install.

<figure><img src="../../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

The following installation options are available:

* **Main Application Configuration:**
  * **Resource Preset:** Select the resource preset (CPU, memory) for the primary n8n service.
  * **Replicas:** Choose between **Fixed Replica Count** or **Autoscaling HPA** for the main application replicas.
  * **Persistent Storage:** Configure persistent storage for the n8n data directory. This is highly recommended for retaining workflows, credentials, and execution logs.
    * **Storage Mount Path:** Specify the path for the persistent storage volume.
* **Worker Configuration:**
  * **Resource Preset:** Select the resource preset for background job processing workers.
  * **Replicas:** Set the number of replicas for the worker instances.
* **Webhook Configuration:**
  * **Resource Preset:** Select the resource preset for dedicated webhook processing instances.
  * **Replicas:** Set the number of replicas for webhook instances.
* **Valkey/Redis Configuration (Internal Cache/Queue):**
  * **Resource Preset:** Select the resource preset for the Valkey/Redis instance used for inter-service communication.
  * **Architecture:** Choose between **Standalone Mode** (single service) or **Replication Mode** (primary and read replicas).
* **Networking Settings:**
  * **HTTP Ingress:** Configure authentication settings for network access. **Apolo Platform Authentication** is enabled by default.
* **Database Configuration:**
  * **Database Configuration:** Choose between **SQLite Database** (local storage, less scalable) or **Postgres Database** (external, recommended for production/replication mode).
    * If choosing Postgres, you must provide **Postgres User Credentials** by choosing an existing PostgreSQL app deployment. Refer to Postgres app documentation for instructions on how to install it.
* **Metadata:** Enter the application display name.

**Step 3** — Click "Install" to initiate deployment. You will be redirected to the application details page where you can monitor the installation progress and view application outputs. For instructions on how to access the application, please refer to the Usage section.

#### Usage

After installation, you can access the n8n web interface to start building and managing your workflows.

To view and manage installed instances of the n8n app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the n8n app you just installed. To open the detailed information, click the **Details** button.

Once in the "Details" page, you can launch the application by clicking the **Open** button next to the app name.

Upon first access, n8n will prompt you to create an admin user (username and password). Once configured, you can access the main dashboard.

You can now start creating, executing, and monitoring your workflow automations for MLOps and other data integration needs.

#### References

* [n8n documentation](https://docs.n8n.io/)
* [Installing n8n using Apolo CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/n8n.md)
* [Managing Apps](../managing-apps.md)
* [PostgreSQL App Documentation](postgre-sql.md)
