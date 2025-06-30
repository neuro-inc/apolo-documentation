# VSCode

## Overview

Visual Studio Code (VSCode) on Apolo is a full-featured, in-browser version of the popular IDE, Visual Studio Code. It is preinstalled with Python extensions and offers seamless MLflow integration, making it an ideal environment for developing, debugging, and managing machine learning projects. For more information about the VSCode app, as well as detailed instruction of how to use it in Apolo Console, visit the main [VSCode app](../../../apolo-console/apps/available-apps/vs-code.md) page.

## Managing application via Apolo CLI

VSCode can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing VSCode using Apolo CLI.

#### Install via Apolo CLI

**Step 1** — Use the CLI command to get the application configuration file template:

```bash
apolo app-template get vscode -o myvscode.yaml
```

**Step 2** — Fill in the application parameters. Here is an example config file with some of those parameters:

```yaml
# Example of myvscode.yaml

template_name: vscode
template_version: latest
display_name: myvscode
input:
  preset:
    name: cpu-medium
  vscode_specific:
    override_code_storage_mount:
  extra_storage_mounts:
    mounts:
    - storage_uri:
        path: storage:my-extra-storage
      mount_path:
        path: /mnt/extra
      mode:
        mode: rw
  networking:
    http_auth: true
```

**Configuration Parameters Explained:**

1. **preset.name**: Specifies the resource preset for the service, e.g., `cpu-medium`.
2. **vscode\_specific.override\_code\_storage\_mount**: Overrides the default storage mount path for the VSCode application. Leave this field empty so you can use the default settings.
3. **extra\_storage\_mounts**: Adds additional storage mounts, allowing integration with Apolo Storage.
4. **networking.http\_auth**: Enables HTTP authentication for added security.

**Step 3** — Install the application into your Apolo project:

```bash
apolo app install -f myvscode.yaml
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

* [Installing VSCode using Apolo Console](../../../apolo-console/apps/available-apps/vs-code.md)
* [Code Server project](https://github.com/coder/code-server)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
