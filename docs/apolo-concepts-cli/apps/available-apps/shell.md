# Shell

## Overview

**Shell** provides a fully-featured, in-browser terminal integrated with the pre-installed and authorized Apolo CLI. For more information about the Shell app, as well as detailed instruction of how to use it in Apolo Console, visit the main [Shell application](../../../apolo-console/apps/available-apps/terminal.md) page.

## Managing application via Apolo CLI

Shell can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing using Apolo CLI.

#### Install via Apolo CLI

**Step 1** — Use the CLI command to get the application configuration file template:

```bash
apolo app-template get shell -o shell.yaml
```

**Step 2** — Fill in the application parameters. Here is an example config file with some of those parameters:

```yaml
# Example of shell.yaml
template_name: shell
template_version: v25.5.1
display_name: myshell
input:
  networking:
    http_auth: true
  preset:
    name: cpu-micro
```

**Configuration inputs explained:**

1. **preset.name**: Specifies the resource preset for the underlying web server, e.g., `cpu-micro`.
2. **networking.http\_auth**: Enables HTTP authentication. Only authorized users will be able to access the application's web server.

**Step 3** — Install the application into your Apolo project:

```bash
apolo app install -f shell.yaml
```

Monitor the application status transitions via CLI:

```bash
apolo app list
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

**Step 4** — To uninstall the application via CLI, use:

```bash
apolo app uninstall <app-id>
```

## References

* [Apolo Shell application](../../../apolo-console/apps/available-apps/terminal.md)
* [Apolo web shell application repository](https://github.com/neuro-inc/web-shell)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
