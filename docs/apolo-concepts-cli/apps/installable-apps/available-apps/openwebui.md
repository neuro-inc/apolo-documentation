# OpenWebUI

### Overview

OpenWebUI is an extensible, feature-rich, and user-friendly self-hosted web interface for interacting with Large Language Models (LLMs). It is designed to operate entirely independent of AI providers such as OpenAI' ChatGPT or Google Gemini, providing a private and secure environment for your AI interactions. OpenWebUI supports various LLM runners, including vLLM, Ollama and other OpenAI API-compatible tools, making it a versatile solution for deploying and managing language models. With an interface similar to ChatGPT, it offers a familiar and intuitive user experience. For more information about this application, refer to the main [OpenWebUI app page](../../../../apolo-console/apps/installable-apps/available-apps/openwebui.md).

### Installing

OpenWebUI can be installed on Apolo either via the CLI or the [Web Console](../../../../apolo-console/apps/installable-apps/available-apps/openwebui.md). Below are the detailed instructions for installing OpenWebUI using Apolo CLI.

#### Install via Apolo CLI

#### Install via Apolo CLI

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get openwebui > openwebui.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file. Note that since this application requires integrations with other apps, you must provide each app's `instance-id` . You can get a list of all installed apps and their instance ids by running `apolo app list` on your terminal.

```yaml
# Example of openwebui.yaml

template_name: openwebui
template_version: v25.7.0
input:
    ingress_http:
        auth: true
    embeddings_api:
        type: app-instance-ref
        instance_id: <instance-id> # example: 2bdb5225-e9f1-4208-a4af-9f7bd21c6d17
        path: $.internal_api
    llm_chat_api:
        type: app-instance-ref
        instance_id: <instance-id>
        path: $.chat_internal_api
    pgvector_user:
        type: app-instance-ref
        instance_id: <instance-id>
        path: '$.postgres_users.users[1]'
    displayName: openwebui-demo
    preset:
        name: cpu-small
    openwebui_specific:
        env: []
```

Explanation of configuration parameters:

* **preset:** hardware preset allocated for the app
* **pgvector\_user:** Select the credentials for the PostgreSQL database that will be used for both the chat history and the vector embeddings.
* **embeddings\_api:** OpenAI Compatible Embeddings API
* **llm\_chat\_api:** OpenAI Compatible Chat API
* **ingress\_http**: enable Apolo authentification

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f openwebui.yaml
```

Monitor the application status using:

```bash
apolo app list
```

To uninstall the application, use:

```bash
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](../../../../apolo-console/apps/installable-apps/available-apps/openwebui.md#usage) section.

### eferences

* [OpenWebUI documentation](https://docs.openwebui.com/)
* [Installing OpenWebUI using Apolo Console](../../../../apolo-console/apps/installable-apps/available-apps/openwebui.md)
