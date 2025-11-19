# Shell

**Shell** provides a fully-featured, in-browser terminal integrated with the pre-installed and authorized Apolo CLI.\
It allows users to run Apolo command-line tools and clients instantly—without needing to install or configure anything on their local machine. Ideal for managing resources, running jobs, and interacting with the platform securely and efficiently.

## Accessing the Shell App

Navigate to the **Apolo Console** and click on the **Apps** section from the left-hand navigation menu. You will see a list of available apps, as shown in the screenshot below.

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption><p>Application gallery</p></figcaption></figure>

Locate the **Shell** app. If it is not yet installed, proceed to the installation steps.

## Installing the Shell App

1. Click on the button **Install** located on the **Shell** app card.
2.  Configure _resources_.

    You will be redirected to the installation page. Under the **Resources** tab, choose a preset configuration based on your computational needs (e.g., `cpu-small`, `cpu-medium`). If you are not planning to run heavy workloads on it, choose the smallest available preset.
3.  Network Settings.

    Shell app comes impersonated with your user credentials and enabled authorization by default. Disabling authorization will allow anyone to access the web CLI. Do it only if you know what you're doing.
4. Add _metadata_.\
   In the next step, under the **Metadata** tab, provide a name for your app instance, otherwise a UUID-based name will be generated.
5. Install the App.\
   Click on the **Install** button to complete the installation process. The app will appear in the **Installed Apps** tab once successfully deployed.

## Managing Installed Terminal Apps

To view and manage installed instances of the Terminal app:

1. Go to the **Installed Apps** tab.
2. You will see a list of running **Shell** app instances. To open the detailed information & uninstall the app, click the **Details** button.

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption><p>Installed Shell app</p></figcaption></figure>

**Details page** contains the next information:

* Application metadata, such as ownership information, date of installation, ID, etc.
* Application status and it's transitions
* Application inputs / settings
* Logs (at the dedicated tab)
* Outputs values

<figure><img src="../../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>Shell application details (1)</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption><p>Shell application details (2)</p></figcaption></figure>

## Using the Terminal App

1.  Launch the **Terminal**.

    Click the **Open App** button at the top of the app details page to launch the web-based shell environment in a new browser tab.

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption><p>Shell application domain address</p></figcaption></figure>

2. Run Commands.\
   Use the terminal to execute shell commands. A welcome message provides helpful examples of commands you can run, such as managing Apolo CLI workflows and running jobs.

**Example:**

<pre class="language-bash"><code class="lang-bash"><strong>apolo config show
</strong></code></pre>

<figure><img src="../../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption><p>Shell application: <code>apolo config show</code> example</p></figcaption></figure>

## References

* [Apolo web shell application repository](https://github.com/neuro-inc/web-shell)
* [Apolo CLI reference](https://app.gitbook.com/o/-MMLX64i1AQdS3ehf2Kg/s/-MOkWy7dB5MDbkSII8iF/)
