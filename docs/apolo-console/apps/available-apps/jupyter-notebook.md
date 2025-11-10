# Jupyter

### Overview

Jupyter Apolo is an application designed to facilitate interactive computing and data analysis. It supports both Jupyter Lab and Jupyter Notebook interfaces, providing a flexible and powerful environment for data scientists and analysts to develop and share their work. With seamless integration into Apolo's ecosystem, it leverages Apolo Storage for storage and MLFlow for experiment tracking, making it an ideal choice for collaborative and scalable data science workflows.

### Key Features

* **Resource Presets**: Choose from predefined resource configurations to optimize performance based on your workload needs.
* **Jupyter Type Selection**: Flexibility to deploy either Jupyter Lab or Jupyter Notebook, catering to different user preferences and project requirements.
* **Storage Integration**: Direct integration with Apolo Storage allows users to mount directories using the `storage:` prefix, facilitating easy data access and management.
* **HTTP Authentication**: Optional HTTP authentication ensures secure access to your Jupyter environment.
* **MLFlow Integration**: Built-in support for MLFlow enables seamless experiment tracking and model management, enhancing reproducibility and collaboration.
* **Extra Storage Mounts**: Attach additional storage volumes to your Jupyter application for expanded data capacity.
* **Kubernetes-Native**: Optimized for deployment within Kubernetes clusters, ensuring scalability and resilience.

### Installing

#### Install via Apolo Web Console

**Step 1** — Navigate to the Apps page, find Jupyter from the list and click the corresponding "Install" button. This will redirect you to the installation page

<figure><img src="../../../.gitbook/assets/image (28) (1).png" alt=""><figcaption><p>Screenshot of Apps page that displays a block with Jupyter app</p></figcaption></figure>

**Step 2** — Configure the application by filling the required fields and click install.

<figure><img src="../../../.gitbook/assets/image (29).png" alt=""><figcaption><p>Jupyter App installation page</p></figcaption></figure>

The following installation options are available:

* **Resource Preset**: Choose from the dropdown menu - this is the only required field that you must set
* **Jupyter App Configuration**:
  * **Container Image** - you can customize the container image used by the application or just use the default one provided by Apolo which comes with the most used Data Science and ML libraries
  * **Override Default Storage Mount** - optionally choose another directory to mount into the application container as the root of your application
  * **Extra Storage Mounts** - optionally mount extra volumes into the application container
* **Networking Settings**
  * **HTTP Authentication** - is enabled by default so you application is only accessible to users authenticated in Apolo and with permissions in the current project
  * **MLFlow Integration** - optionally inject a running MLFlow server configuration into the application
* **Metadata**: Enter the application display name.

**Step 3** — Click "Install" to initiate deployment. You will be redirected to the application details page where you can monitor the installation progress and view application outputs. For instructions on how to access the application, please refer to the [Usage](jupyter-notebook.md#usage) section.

### Usage

After installation, you can utilize Jupyter for various data science tasks and you can access it directly from your browser.

To view and manage installed instances of the Jupyter app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Jupyter** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

<figure><img src="../../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Once in the Details page, click the **Open App** button at the top of the page to launch the application in a dedicated browser window.

<figure><img src="../../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

You can now access Jupyter to run experiments, train models and collaborate with colleagues.

<figure><img src="../../../.gitbook/assets/image (32) (1).png" alt=""><figcaption></figcaption></figure>

#### Example Workflow

1. **Start a Jupyter Session**: Access Jupyter Lab or Notebook through the provided URL.
2. **Data Analysis**: Load datasets from Apolo Storage and perform data analysis using Python or R.
3. **Experiment Tracking**: Utilize MLFlow integration to track experiments and manage model versions.
4. **Collaboration**: Share notebooks and results with team members within the Apolo platform.
