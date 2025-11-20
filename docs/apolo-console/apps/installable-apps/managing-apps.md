# Managing Apps

The Apolo platform provides a rich catalog of applications to support your entire MLOps lifecycle.&#x20;

Installable applications in Apolo platform are made of various shapes and forms. Some of them are acting mostly as a metadata holders only to be consumed by other applications, like Huggingface or DockerHub apps. Others might be a full-fledged platforms with internal workload management and scheduling like N8n or Dify.&#x20;

This guide is an overview of applications lifecycle management that include catalogue exploration to find needed application, it's installation, reconfiguration, debugging and cleanup when not needed.

## **Explore available apps**

The "All Apps" page is your central hub for discovering applications available on the platform.

1. Navigate to the **Apps** section from the main menu on the left.
2. You will see the **All Apps** page, which displays a catalog of available applications like Jupyter, Stable Diffusion, MLFlow Core, and many more.
3.  Scroll through the list to see all available options.

    <figure><img src="../../../.gitbook/assets/image (28) (2).png" alt=""><figcaption><p>Application list page</p></figcaption></figure>
4. Each application card has an **Explore** button. To learn more about an application before installing it, click **Explore**.
5. This will take you to the application's detail page, where you can find:
   * A detailed description of the app.
   * Version picker.
   * Various links to related materials like **Documentation** and **References**.
   *   A list of **Related** applications — applications that work together with the current one.

       <figure><img src="../../../.gitbook/assets/image (2) (3).png" alt=""><figcaption><p>MLFlow application explore page</p></figcaption></figure>

## **Install app**

You can install an application directly from the Apolo web console or [using CLI](../../../apolo-concepts-cli/apps/installable-apps/managing-apps.md).

A common installation flow includes selection of Application and it's version, filling in required configurations and triggering the installation. Under the hood, just like with [jobs](../pre-installed/jobs/), many things happens — from resources allocation and infrastructure setup to scheduling and monitoring.

1. Locate the application you wish to install in the **All Apps** list.
2. Click the **Install** button on the application's card to install the latest app version. Alternatively, if you wish to install an older version, click **Explore** button, select needed version and click install being on  this page. This will redirect your browser to installation window.
3. You will be directed to the installation configuration page. Here, you must define the parameters for your app deployment.
4. Fill in the required fields. Common parameters include:
   * **Resource Preset**: Choose the CPU, memory, and GPU resources for your application components. Clicking this field opens a modal where you can select from a list of available presets in current environment.
   * **Storage and Networking**: Configure storage mounts and networking settings like HTTP authentication if needed.
   *   **Metadata**: Assign a unique name to your application instance.

       <figure><img src="../../../.gitbook/assets/image (3) (3).png" alt=""><figcaption><p>Installation page for Jupyter app</p></figcaption></figure>
5. After configuring all the necessary parameters, click the final **Install** button at the bottom of the page to deploy the application. You will be redirected to that app instance's Details page.

## **Manage installed app**

Once an application is installed, you can manage it from the "Installed Apps" tab.

1. On the main **Apps** page, click the **Installed Apps** tab.
2. This page lists all applications currently running in your selected project. Each app card displays its status transitions (e.g., Healthy, Degraded).
3. Click the **Details** button on the card of the application you want to inspect and manage.

### Application details

The application's details page provides comprehensive information about the running instance:

* **Details tab**: Shows metadata like app type, version, instance ID, owner, inputs and outputs, e.g.
* **Observability tab:** Allows user to check application instance status changes, open monitoring dashboards and fetch debugging information in case of issues.
* **Log tab**: View real-time and historical logs from the application to monitor its behavior and troubleshoot issues.

### Application configuration

After installing the app, you can update it's inputs and display name as needed. For it, navigate to application details page and click **Configure** button. Here you will be displayed current input values used to run the application.

<div data-full-width="false"><figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Application configure button</p></figcaption></figure></div>

Some of the input values cannot be reconfigured like PostgreSQL server version due to compatibility and not to break the application instance. You might not be able to edit those inputs. If you are forced to change such parameter, you will need to reinstall the application.

<figure><img src="../../../.gitbook/assets/image (23).png" alt=""><figcaption><p>Application configure: non-configurable parameters</p></figcaption></figure>

Always check application inputs description before re-configuring the application, since we can not forecast update impact for all cases (consider changing server version that uses ORM without running the migration).

If something goes wrong during the update, consider rolling back to the previous inputs version by performing reconfiguration using previous inputs.

## **Use installed app**

After installing an application, you can use it depending on its type. Some of the apps do not provide any direct usage endpoints as mentioned above, like DockerHub app. Check particular application documentation in [available-apps](available-apps/ "mention") to see it's usage examples.

### Apps with Web Interfaces

For applications with web interfaces (like Jupyter, Stable Diffusion, Fooocus, OpenWebUI, VS Code, etc.):

1. Navigate to the **Installed Apps** page and click **Details** on the desired application.
2. Click the **Open App** button at the top of the app details page to launch the application in a new browser tab.

### API-only apps

For API-only applications (like LLM inference models, embeddings services):

1. Navigate to the **Installed apps** page and click **Details** on the desired application.
2. On the "Details" tab, scroll down to the **Output** section.
3. Look for an **HTTP API** entry that contains the API endpoint URL.
4. Copy the **Hostname** URL to use in your API calls.

## Uninstall app

To uninstall the application instance, navigate to it's Details tab and click the **Uninstall** button. This action requires write permission to the project.

If the application has dependent apps (other apps that consume inputs from this application), the uninstall will not be allowed unless forced by the user. In this case, cascade apps removal will be performed, therefore, use with caution!

## References

* [Apps vs Jobs](../../../getting-started/apolo-jobs-vs-apps.md)
* [Available apps](available-apps/)
* [Managing apps](managing-apps.md)
* [Managing apps (CLI)](../../../apolo-concepts-cli/apps/installable-apps/managing-apps.md)
