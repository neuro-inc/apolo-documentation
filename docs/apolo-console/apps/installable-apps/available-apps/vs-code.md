# VSCode

### Overview

Visual Studio Code (VSCode) on Apolo is a full-featured, in-browser version of the popular IDE, Visual Studio Code. It is preinstalled with Python extensions and offers seamless MLflow integration, making it an ideal environment for developing, debugging, and managing machine learning projects. Hosted on Apolo, a scalable MLOps platform, VSCode provides a familiar and powerful development environment accessible directly from your browser.

### Key Features

* **In-Browser Development Environment**: Access a full-featured version of VSCode directly from your browser, eliminating the need for local installations.
* **MLflow Integration**: Easily track and manage machine learning experiments with built-in MLflow support.
* **Apolo Storage Integration**: Mount directories from Apolo Storage using the `storage:` prefix, facilitating seamless data management.
* **Resource Presets**: Configure resource allocation per service replica to optimize performance and cost.
* **HTTP Authentication**: Secure your development environment with optional HTTP authentication.
* **Custom Storage Mounts**: Override default storage mounts or add additional storage paths as needed.
* **Networking Settings**: Configure network settings to suit your deployment requirements.

### Installing

Below are the detailed instructions for installing VSCode using Apolo Console. For instructions on how to install it using Apolo CLI, visit [Apolo CLI VSCode page](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/vs-code.md).

#### Install via Apolo Web Console

**Step 1** — Navigate to the list of applications and find Visual Studio Code. Click the "Install" button.

**Step 2** — Configure the application parameters:

* **Resource Preset**: Select the desired resource preset from the dropdown menu.
* **VSCode App**: Configure storage mounts and overrides as needed.
* **Networking Settings**: Enable HTTP Authentication if required.
* **Metadata**: Enter the application display name.

**Step 3** — Click "Install" to proceed with the installation. You will be redirected to the application details page, displaying inputs, outputs, and health status.

### Usage

After installing the VSCode application, you can access it directly from your browser. Click the **Open App** button at the top of the app details page to launch VSCode.

<figure><img src="../../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### References

* [Installing VSCode using Apolo CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/vs-code.md)
