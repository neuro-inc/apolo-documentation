# Superset

### Overview <a href="#overview" id="overview"></a>

**Apache Superset** is a modern, enterprise-ready business intelligence web application for data exploration and visualization. Designed for scalability, extensibility, and ease of use, Superset supports a wide range of SQL-speaking databases and enables teams to create rich dashboards and interactive charts without writing code. Built with a lightweight architecture and a user-friendly interface, Superset is ideal for organizations seeking powerful, open-source BI tooling.

For more information about the Superset app, as well as detailed instruction of how to use it in Apolo Console, visit the main Superset page.

### Managing application via Apolo CLI <a href="#managing-application-via-apolo-cli" id="managing-application-via-apolo-cli"></a>

Shell can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing using Apolo CLI.

**Install via Apolo CLI**

**Step 1** — Use the CLI command to get the application configuration file template:

```
apolo app-template get superset > superset.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Application template configuration for: superset
# Fill in the values below to configure your application.
# To use values from another app, use the following format:
# my_param:
#   type: "app-instance-ref"
#   instance_id: "<app-instance-id>"
#   path: "<path-from-get-values-response>"

template_name: superset
template_version: v25.7.0
input:
  # Enable access to your application over the internet using HTTPS.
  ingress_http:
    # Require authenticated user credentials with appropriate permissions for all incoming HTTPS requests to the application.
    auth: true
  # Set the configuration for Superset worker.
  worker_config:
    # Select the resource preset used per service replica.
    preset:
      # The name of the preset.
      name: ''
  # Set the configuration for Superset Web UI.
  web_config:
    # Select the resource preset used per service replica.
    preset:
      # The name of the preset.
      name: ''
  # Set your own postgres as a database for Superset app. If it was not provided, postgres instance will be created within the same app.
  postgres_config:
    user: ''
    password: ''
    host: ''
    port: 8080
    pgbouncer_host: ''
    pgbouncer_port: 0
    dbname:
    jdbc_uri:
    pgbouncer_jdbc_uri:
    pgbouncer_uri:
    uri:
    # Configuration for the Postgres connection URI.
    postgres_uri:
  # Set the admin user fields to be
  admin_user:
    # Set Admin Username.
    username: admin
    # Set Admin first name.
    firstname: Superset
    # Set Admin last name.
    lastname: Admin
    # Set Admin email.
    email: admin@superset.com
    # Set Admin password.
    password: admin
```

Explanation of configuration parameters:

1. **Resource Preset**: Choose a compute preset to define the hardware resources allocated. Example: `H100x1`
2. **HTTP Authentication**: Enabled for secure access.
3. **Admin User:** Set admin login credentials

**Step 3** — Deploy the application in your Apolo project:

```
apolo app install -f superset.yaml
```

Monitor the application status using:

```
apolo app list
```

To uninstall the application, use:

```
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](superset.md#usage) section.

### Usage

After installation, you can utilize Superset for image generation workflows and you can access it directly from your browser.To view and manage installed instances of the Superset app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Superset** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details" page, scroll down to the Outputs sections. To launch the applications, find the **HTTP API** output with with the public domain address, copy and open it in a dedicated browser window.

Connect the data sources, build charts, and dashboards.

<figure><img src="../../../../.gitbook/assets/image (54).png" alt=""><figcaption><p>Superset UI</p></figcaption></figure>

### References:

* [Apache Superset Documentation](https://superset.apache.org/docs/intro)
* [Apache Superset UI installation](superset.md)
* [Apache Superset source code](https://github.com/apache/superset)
* [Managing Apps](../managing-apps.md)
* [App Cli documentation](../../)
