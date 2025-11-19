# Launchpad

## **Overview**

**Launchpad** is an application gateway for the Apolo MLOps platform that enables Apolo users to securely expose platform applications to end-users through customizable authentication powered by Keycloak integration.

With Launchpad, you can share access to applications deployed on Apolo—including pre-built apps like OpenWebUI (a user-friendly interface for interacting with LLMs)—without requiring end-users to have full Apolo accounts. Additionally, Launchpad allows you to deploy custom applications using Apolo's Service Deployment feature and expose them to your users with personalized branding, including custom names, descriptions, and logos.

**Key capabilities include:**

* **Bring Your Own Authentication:** Deploy and manage a dedicated Keycloak instance for user authentication, with support for user management, role-based access control, groups, and integration with external identity providers (OIDC, OAuth2)
* **Flexible App Deployment Models:**
  * **Shared Applications:** Deploy a single app instance accessible to all users, with the application leveraging user identity information provided by Keycloak (ideal for apps like OpenWebUI that support multi-user collaboration)
  * **Per-User Applications:** Automatically provision isolated app instances for each user, ensuring complete data separation and personalized environments
* **Custom Branding:** Import custom applications or existing app instances with tailored metadata—display names, descriptions, and visual identities—to create a polished experience for your end-users
* **Flexible App Exposure:** Share both platform-native Apolo applications and custom-deployed services through a unified, user-friendly interface
* **Quickstart Presets:** Deploy pre-configured application stacks like OpenWebUI with all dependencies in minutes
* **External User Access:** Provide seamless application access to clients, stakeholders, or team members outside your Apolo organization through Keycloak-managed authentication

***

## Installing Launchpad

Launchpad can be installed from the Apolo Console interface or using the Apolo CLI.

### Installation via Apolo Console

1. **Navigate to the Apps page** in the Apolo Console.
2. **Find the Launchpad App** and click **Install**.
3. **Configure Launchpad Preset:**
   * Choose a suitable **Resource Preset** for the Launchpad application itself (e.g., `cpu-medium` or higher).
4. **Choose Quickstart Apps:**
   * **No Startup Apps:** Installs only Launchpad, allowing you to add applications later.
   * **OpenWebUI:** Installs OpenWebUI and its dependencies upon Launchpad startup, providing a pre-configured LLM chat interface.

**OpenWebUI Configuration (Optional)**

If you select the **OpenWebUI**, you need to configure the following dependencies:

* **LLM Configuration:**
  * **Pre-configured HuggingFace LLM Model:** Select the desired LLM model (e.g., `meta-llama/Llama-3.1-8B-Instruct`).
  * **Hugging Face Token (Optional):** If using gated or private models, provide a Hugging Face API token stored as an Apolo Secret (`HF_TOKEN`).
  * **LLM Preset:** Choose a resource preset for the LLM model (typically a GPU-enabled preset, e.g., `gpu-a100-x1`).
* **Postgres Configuration:**
  * **Postgres Preset:** Choose a resource preset for the Postgres database (e.g., `cpu-medium`).
  * **Postgres Replicas:** Set the number of replicas (e.g., `1`).
* **Text Embeddings Configuration:**
  * **Text Embeddings Preset:** Choose a resource preset for the embeddings service (e.g., `gpu-l4-x1`).
  * **Embeddings Model:** Select a pre-configured Hugging Face model for text embeddings (e.g., `BAAI/bge-m3`).

5. **Metadata (Optional):** You can customize the app display name.
6. **Click Install.**

Be aware that when choosing the OpenWebUI Quick Start App option, Apolo will also install vLLM, Text Embeddings and Postgresql apps, which will consume credits as well.

### Installation via Apolo CLI

Currently, due to a known bug in the UI, if you choose the OpenWebUI Quick Start App, it's recommended to export the configuration and install it using the Apolo CLI:

1. **In the Launchpad App installation page**, select the **OpenWebUI** Quick Start App and configure your options.
2. Scroll to the bottom and click **Export App Configuration** (download icon).
3. Open your terminal and install the app using the downloaded configuration file:

```bash
apolo app install --file launchpad-custom-app-installation-config.yaml
```

Refer to [Launchpad CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/launchpad.md) page to learn more about installing and managing Launchpad using the CLI.

***

## Managing Launchpad and Keycloak Users

Once Launchpad is installed, you can access the installed app on the **Installed Apps** page. Click **Open** to access various Launchpad URLs.

### Accessing the Launchpad Interface

1. Click **Open** on the Launchpad app card.
2. Select **App URL**.
3.  The Launchpad interface loads, requiring a login.\\

    <figure><img src="../../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>
4.  Launchpad comes with a default admin user that can manage what apps are available through Launchpad UI. You can find this admin user's credentials by scrolling down the Launchpad instance details page in Apolo and copying the **Password** field in **Launchpad Default Admin User**\\

    <figure><img src="../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
5.  Use these credentials to login to Launchpad. You should see a list of available apps - only OpenWebUI for now\


    <figure><img src="../../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

### Managing Users via Keycloak

Launchpad uses Keycloak for authentication and user management.

1. **Access Keycloak:**
   * On the Launchpad app card (in Apolo), click **Open**.
   * Select **Keycloak Config > Web App URL**.
2. **Retrieve Admin Credentials:**
   * Go to the **Launchpad Details** page in the Apolo Console.
   * Under the **Output** section, find **Launchpad Default Admin User** to retrieve the default username (`admin`) and **Password**.
   * Scroll down to **KeycloakConfig** to find the **Keycloak Admin Password** (used for the Keycloak administrative interface).
3. **Log into Keycloak:**
   * Use the Keycloak Admin Password and the username `admin` to sign in to the Keycloak Administration page.
4. **Manage Users and Groups:**
   * **Add User:** Navigate to **Users** and click **Add user** to create new users.
   * **Manage Roles and Groups:** Navigate to **Groups** to create groups and assign roles/permissions to users.
5. **Enable New Sign-Ups (for OpenWebUI):**
   * If you deployed OpenWebUI, ensure users can sign up:
     * Navigate to **OpenWebUI** in the Installed Apps list.
     * Open the **Admin Panel** (available to the admin user).
     * Go to **Settings > Authentication**.
     * Toggle **Enable New Sign Ups** to `On`.
     * **Save** settings.

***

## Importing Apps

### Importing Apps using the Admin Panel

Launchpad comes with a Admin Panel that allows you to manage app templates and running app instances directly through the Launchpad Admin Panel.

{% embed url="https://drive.google.com/file/d/1uxVy_Ry7wX3HShMURBE6mC3WjKmr6Dy6/view?t=8" %}
Video Demo
{% endembed %}

To Access the Admin Pane&#x6C;**:** Log into Launchpad using the default admin credentials (available in app outputs) and click **Admin Panel** (top right).

<div align="left"><figure><img src="../../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure></div>

\
This is what the Admin Panel looks like when there are no templates or instances imported.\


<div align="left"><figure><img src="../../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure></div>

#### **Import a New Template:**

* Obtain an App Template by following [this guide](launchpad.md#obtaining-and-preparing-app-templates-for-launchpad).
*   Click **Import Template**.\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure></div>
* Click **Upload File** and select the `.yaml` configuration file you prepared in the previous step.
* The fields will automatically populate with information from the Apolo Apps API. You can manually edit the:
  * **Display Name**
  * **Logo URL** (as demonstrated in the video)
  * **Short/Long Description**
  * **Tags**
  * **Input (JSON):** Modify default configuration values.
* Check or uncheck **Shared** based on whether you want a single instance for all users (Shared) or a new instance provisioned for each user (Not Shared).
*   Click **Import Template**. The new template will appear under **App Templates**.\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure></div>
*   After importing, the app will be visible in the main Launchpad interface, ready for users to click **Open** and launch their instance.\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure></div>


*   After installing the app by clicking "Open", the new app instance will also appear in the list of App Instances on the Admin Page\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure></div>

Using the App Templates list, you can:

* **View:** Displays all metadata and configuration associated with the template.
* **Edit:** Allows modification of the display name, logo, descriptions, tags, and sharing settings.
* **Delete:** Removes the template. **Warning:** Deleting a template will uninstall all associated running instances.

#### **Importing a Running App Instance**

You can also import apps that are already running in Apolo into Launchpad.

* Install a Service Deployment app by following [this guide](launchpad.md#deploying-a-service-deployment-app-that-uses-launchpad-authentication).
* Click **Import App Instance**.
*   The panel will load all running apps in your current Apolo project.\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure></div>
* Select the desired running app instance (e.g., a Service Deployment).
* Configure the display details (Name, Logo URL, Description).
  * You can customize the app's name, description, logo and etc. For the purposes of this tutorial, we will rename this app to My Custom App
*   Click **Import App**. The running app instance will now be visible in the main Launchpad interface under its new name and accessible to authenticated Keycloak users.\


    <div align="left"><figure><img src="../../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure></div>

Using the App Instances list you can:

* **Open:** Directly navigates to the running application URL.
* **Delete:** Uninstalls the specific app instance from Apolo.

### Importing Apps using Admin API

Refer to [Launchpad CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/launchpad.md#importing-apps-using-admin-api) page to learn more about installing and managing Launchpad using the CLI.

***

## Summary

The Launchpad app transforms the Apolo MLOps platform into a user-friendly deployment environment, simplifying application access and user authentication for internal and external users via Keycloak integration. By enabling the import of custom app templates and existing app instances, Launchpad offers a versatile solution for showcasing and managing applications within your ecosystem.

## Quick Guides

#### Obtaining and Preparing App Templates for Launchpad

Before importing a custom application template into Launchpad using the API, you must first obtain the application's configuration file from the Apolo Console and convert it to the required JSON format.

**Downloading the App Configuration File**

This process generates a YAML configuration file based on the app's standard installation page, pre-filled with the Launchpad authentication settings.

1. **Navigate to the App Installation Page:** In the Apolo Console, go to the **All Apps** page and select the application you wish to import (e.g., **Visual Studio Code**). Click **Install**.
2. **Configure Resource Presets:** Select the necessary resource presets for the application.
3. **Configure Authentication:** Scroll down to the **Networking Settings** section and configure the HTTP Ingress:
   * Change the **Authentication** dropdown to **Custom Authentication**.
   * In the **Custom Authentication** section, click **Choose App** next to the `AuthIngressMiddleware` to integrate Launchpad.
   * Select the installed `Launchpad` instance and click **Apply**.
4.  **Download Configuration:** Scroll to the bottom of the installation page. Click the **Export App Configuration** download icon (a down arrow pointing into a cloud or similar icon, typically next to the "Install" button) to download the `.yaml` configuration file.\
    \
    The following yaml file is an example Downloaded from the VSCode App installation page.\


    ```yaml
    display_name: ""
    template_name: vscode
    template_version: v25.10.1
    input:
        vscode_specific:
            override_code_storage_mount: null
        extra_storage_mounts: null
        mlflow_integration: null
        preset:
            name: cpu-small
        networking:
            ingress_http:
                auth: {
                    middleware: {
                        type: app-instance-ref, 
                        instance_id: "<launchpad-app-instance-id>", 
                        path: $.auth_middleware
                    }, 
                    type: custom_auth
                }
    ```

**Converting the YAML Configuration to JSON**

If you are using Launchpad API to import templates, follow the instructions below to convert the exported `yaml` file into `json`

The Launchpad API requires the app configuration payload to be in JSON format. Use a command-line tool like `yq` or a combination of `cat` and `python` to perform this conversion.

**Method A: Using `python`**

If you don't have `yq`, you can use Python's built-in YAML and JSON libraries:

1.  **Install PyYAML:** If you haven't already, install the necessary library:

    ```bash
    pip install PyYAML
    ```
2.  **Run the Conversion Script:**

    ```bash
    # Replace [FILE_NAME] with the name of your downloaded YAML file
    python -c "import yaml, json, sys; d=yaml.safe_load(sys.stdin.read()); print(json.dumps(d))" < [FILE_NAME].yaml
    ```

**Method B: Using `yq`**&#x20;

If you have `yq` [installed](https://github.com/mikefarah/yq), this is the simplest method:

```bash
# Replace [FILE_NAME] with the name of your downloaded YAML file
cat [FILE_NAME].yaml | yq -o json
```

The output will be the single-line JSON string containing the app template data, which is ready to be used in the API call to import the template into Launchpad.

Here is the resulting json file.

```json
{
  "display_name": "",
  "template_name": "vscode",
  "template_version": "v25.10.1",
  "input": {
    "vscode_specific": {
      "override_code_storage_mount": null
    },
    "extra_storage_mounts": null,
    "mlflow_integration": null,
    "preset": {
      "name": "cpu-small"
    },
    "networking": {
      "ingress_http": {
        "auth": {
          "middleware": {
            "type": "app-instance-ref",
            "instance_id": "<launchpad-app-instance-id>",
            "path": "$.auth_middleware"
          },
          "type": "custom_auth"
        }
      }
    }
  }
}

```

#### Deploying a Service Deployment App that uses Launchpad authentication

After installing Launchpad, you will be able to deploy Apps that require Launchpad authentication. This small guide will walk you through how you can deploy your custom application using the Service Deployment App.

* Navigate to Apolo Console's **Home Page**, find the Service Deployment app and click **Install**
* Choose a **Resource Preset** (e.g., `cpu-small`).
* **Container Image:** Provide a container image (e.g., `nginx`).
* **Network Configuration:**
  * Enable **HTTP Ingress**.
  * Change **Authentication** to **Custom Authentication**.
  * In **Authentication Middleware**, click **Choose App** and select **Launchpad** with the path&#x20;
  * **Install** the application.
* Import this App Instance into Launchpad so that it enables authentication
  * Follow this guide for Admin Panel usage
  * Follow this guide for API usage

## References

* [Keycloak documentation](https://www.keycloak.org/documentation)
* [OpenWebUI app documentation](openwebui.md)
* [Managing Apps](../../../../apolo-concepts-cli/apps/)
