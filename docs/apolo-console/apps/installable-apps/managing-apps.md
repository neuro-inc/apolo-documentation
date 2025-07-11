# Managing Apps

The Apolo platform provides a rich catalog of applications to support your entire MLOps lifecycle. This guide will walk you through exploring, installing, and managing these applications within your projects.

## **Exploring Apps**

The "All Apps" page is your central hub for discovering applications available on the platform.

1. Navigate to the **Apps** section from the main menu on the left.
2. You will see the **All Apps** page, which displays a catalog of available applications like Jupyter, Stable Diffusion, MLFlow Core, and more.
3.  Scroll through the list to see all available options.\


    <figure><img src="../../../.gitbook/assets/image (1) (3).png" alt=""><figcaption><p>Application list page</p></figcaption></figure>
4. Each application card has an **Explore** button. To learn more about an application before installing it, click **Explore**.
5. This will take you to the application's detail page, where you can find:
   * A detailed description of the app.
   * Version information.
   * Links to official **Documentation** and **References**.
   *   A list of **Related** applications.\


       <figure><img src="../../../.gitbook/assets/image (2) (3).png" alt=""><figcaption><p>MLFlow application explore page</p></figcaption></figure>

## **Installing Apps**

You can install an application directly from the catalog or from its exploration page.

1. Locate the application you wish to install in the **All Apps** list.
2. Click the **Install** button on the application's card. (Alternatively, you can click **Install** from the app's "Explore" page).
3. You will be directed to the installation configuration page. Here, you must define the parameters for your app deployment.
4. Fill in the required fields. Common parameters include:
   * **Resource Preset**: Choose the CPU, memory, and GPU resources for your application. Clicking this field opens a modal where you can select from a list of predefined presets.
   * **Storage and Networking**: Configure storage mounts and networking settings like HTTP authentication if needed.
   *   **Metadata**: Assign a unique name to your application instance.\


       <figure><img src="../../../.gitbook/assets/image (3) (3).png" alt=""><figcaption><p>Installation page for Jupyter app</p></figcaption></figure>
5. After configuring all the necessary parameters, click the final **Install** button at the bottom of the page to deploy the application. You will be redirected to that app instance's [Details](managing-apps.md#app-instance-details) page.

## **Managing Installed Apps**

Once an application is installed, you can manage it from the "Installed Apps" tab.

1. On the main **Apps** page, click the **Installed Apps** tab.
2. This page lists all applications currently running in your selected project. Each app card displays its status (e.g., Healthy, Degraded).
3. Click the **Details** button on the card of the application you want to inspect.

### App Instance Details

The application's details page provides comprehensive information about the running instance:

* **Details Tab**: Shows metadata like the installation ID, owner, and status. It also displays the **Input** parameters used during installation and the **Output** details, such as API endpoints.
* **Log Tab**: View real-time logs from the application to monitor its behavior and troubleshoot issues.
* **Uninstall**: From the "Details" tab, you can click the **Uninstall** button to remove the application instance.

## **Accessing Installed Apps**

If your installed application has a web interface (like Jupyter, Stable Diffusion, or a deployed model), you can access it directly through its public URL.

1. Navigate to the **Installed Apps** page and click **Details** on the desired application.
2. On the "Details" tab, scroll down to the **Output** section.
3. Look for an **HTTP API** entry that contains a public-facing URL (e.g., one ending in `apps.your-org.com`).
4. Copy the **Hostname** URL provided.
5. Paste this URL into a new browser tab. This will open the application's user interface.

...
