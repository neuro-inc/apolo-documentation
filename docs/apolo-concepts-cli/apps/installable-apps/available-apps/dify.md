# Dify

## Overview

Dify is an open-source development platform for building, managing, and deploying applications powered by large language models (LLMs). To learn more about this application, refer to the [Dify](../../../../apolo-console/apps/installable-apps/available-apps/dify.md) documentation page for Apolo Console.

## Installing

Here we provide brief description of the application installation using Apolo CLI. See [managing-apps.md](../managing-apps.md "mention") page for generic flow of application installation via CLI.

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get dify -o dify.yaml
```

Another way is to&#x20;

**Step 2** — Customize the application parameters. Dify requires PostgreSQL for data storage, Apolo Blobs for binary objects store (created implicitly) to begin with. vLLM for and Text Embeddings are also needed but not obligatory to run the application installation.

\
Below is an example configuration file that deploys a Dify application

```yaml
# Example of dify.yaml

template_name: dify
template_version: apolo
display_name: my-dify
input:
  api:
    replicas: 1
    preset:
      name: cpu-medium
  worker:
    replicas: 1
    preset:
      name: cpu-medium
  web:
    replicas: 1
    preset:
      name: cpu-small
  external_postgres:
    type: app-instance-ref
    instance_id: ee62be03-7013-4ef0-8ff8-bf261db4c717 # postgres app instance ID
    path: postgres_users/users/0
  external_pgvector:
    type: app-instance-ref
    instance_id: ee62be03-7013-4ef0-8ff8-bf261db4c717 # postgres app instance ID
    path: postgres_users/users/0
  ingress_http:
    auth: false
  proxy:
    preset:
      name: cpu-micro
  redis:
    master_preset:
      name: cpu-medium

```

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f dify.yaml
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

For instructions on how to access the application, please refer to the [Usage](../../../../apolo-console/apps/installable-apps/available-apps/privategpt.md#usage) section in web console.

## Cleanup

When the application is not needed anymore, you could remove it by clicking the "uninstall" button on the installed app details/status screen.

## References

* [Apolo CLI Application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
* [Apolo CLI Application template commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo web console Dify application management](../../../../apolo-console/apps/installable-apps/available-apps/dify.md)
* [Dify helm chart documentation](https://github.com/neuro-inc/dify-helm)
* [Dify platform documentation](https://docs.dify.ai/)
* [Dify knowledge concept documentation](https://docs.dify.ai/en/guides/knowledge-base/readme)&#x20;
* [PostgreSQL application management](../../../../apolo-console/apps/installable-apps/available-apps/postgre-sql.md)
* [Apolo Buckets application](../../../../apolo-console/apps/pre-installed/buckets.md)
* [Text Embeddings application management](../../../../apolo-console/apps/installable-apps/available-apps/text-embeddings-inference.md)
* [vLLM application management](../../../../apolo-console/apps/installable-apps/available-apps/llm-inference/)
