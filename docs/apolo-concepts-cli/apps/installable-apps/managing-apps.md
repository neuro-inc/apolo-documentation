# Managing Apps

The Apolo CLI provides a powerful and efficient way to manage your applications directly from the terminal. You can explore, install, monitor, and uninstall applications using a set of simple commands.

## **Exploring Available Apps**

Before installing an application, you can explore the full catalog of available app templates.

To list all available application templates, run the following command:

```bash
apolo app-template ls
```

This command will fetch and display a table of all templates you can install, including their name, version, and description.

```
 Name               Title              Version   Description                                  Tags
 ------------------ ------------------ --------- -------------------------------------------- ----------------------------------
  dify               Dify               apolo     Run Dify an open-source, no-code/low-code... Dify, LLM, LLMOps, Rag, Agent
  dockerhub          DockerHub          v25.5.0   Access Images from your private DockerHub... DockerHub, MLOps, Container registry
  jupyter            Jupyter            v25.5.1   Web-based interactive development env...     Jupyter, Notebooks, EDA, ETL, ...
  ...
```

## **Installing an App from a Template File**

The most robust way to install applications via the CLI is by using a YAML configuration file. This allows you to define all installation parameters declaratively.

1.  **Create a YAML file** (e.g., `vscode.yaml`) defining your application's configuration. This includes the template name, resource presets, storage mounts, and other specific inputs.

    **Example `vscode.yaml`:**

    ```yaml
    # Example of myvscode.yaml
    template_name: vscode
    template_version: latest
    display_name: myvscode
    input:
      preset:
        name: cpu-medium
      vscode_specific:
        extra_storage_mounts:
          mounts:
            - storage_uri: storage:my-extra-storage
              mount_path: /mnt/extra
              mode: rw
      networking:
        http_auth: true
    ```
2.  **Run the install command**, pointing to your YAML file using the `--file` flag:

    ```bash
    apolo app install --file vscode.yaml
    ```

    Upon execution, the CLI will confirm the installation:

    ```
    App installed from vscode.yaml
    ```

## **Managing Installed Apps**

Once an app is installed, you can list, monitor, and manage it. To see all applications currently installed in your project, run:

```bash
apolo app ls
```

This command displays a list of your app instances, their unique ID, and their current `State` (e.g., `progressing`, `healthy`, `errored`).

```
 ID                                     Name                       Display Name      Template    Version   State
 ------------------------------------   -------------------------- ----------------- ----------- --------- ------------
  5a5fa379-4635-4418-959f-457b44280c21   apolo-demo-user-vscode...  myvscode          vscode      v25.5.1   healthy
  c4bd3c55-1985-4525-9cf9-f8453b...      apolo-demo-user-hugging... Hugging Face Cache  hugging-face-cache HEAD      healthy
  ...
```

### **Viewing App Logs**

To view the logs for a running application, use the `logs` command with the application's `ID` (which you can get from `apolo app ls`).

```bash
# First, get the App ID
apolo app ls

# Then, view the logs
apolo app logs 5a5fa379-4635-4418-959f-457b44280c21
```

The CLI will stream the application's logs directly to your terminal.

### **Uninstalling an App**

To remove an application instance, use the `uninstall` command with the application's `ID`.

```bash
apolo app uninstall 5a5fa379-4635-4418-959f-457b44280c21
```

## **Accessing an Installed App**

For applications with a web interface (like VS Code, Jupyter, or a deployed model), you need to retrieve their public URL.

1.  Use the `get-values` command with the application's `ID`:

    ```bash
    apolo app get-values <app-id>
    ```
2.  This command returns a table of output values for the app, including API endpoints. Look for the path `external_web_app_url`.

    ```
     App Instance ID                        Type      Path                    Value
     ------------------------------------   -------   ----------------------- ----------------------------------------------------
      5a5fa379...c21                         RestAPI   external_web_app_url    {"host": "vscode--5a5fa379...c21.apps.apolo.us", "port": 80, ...}
      5a5fa379...c21                         RestAPI   internal_web_app_url    {"host": "apolo-demo-user-vscode-5a5fa379-custom...", "port": 8080, ...}
    ```
3. Copy the `host` URL from the `external_web_app_url` value and open it in your browser to access the application's interface.
