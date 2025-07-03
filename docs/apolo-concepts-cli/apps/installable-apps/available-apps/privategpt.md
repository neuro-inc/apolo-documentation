# PrivateGPT

## Overview

**PrivateGPT** on Apolo gives you a turnkey, fully private solution to ingest, index, and query your data using LLMs — powered by embeddings and a PGVector backend. It’s perfect for building chatbots over proprietary docs, internal knowledge search tools, and RAG workflows—all while maintaining complete data sovereignty. To learn more about PrivateGPT, refer to the main [PrivateGPT](../../../../apolo-console/apps/installable-apps/available-apps/privategpt.md) page for Apolo Console.

## Installing

### Installing with Apolo CLI

As for the approach of managing Service Deployment applications via Apolo Console, see dedicated the instructions on a [documentation dedicated page](../../../../apolo-console/apps/installable-apps/available-apps/privategpt.md).

## Usage

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get private-gpt -o myservice.yaml
```

**Step 2** — Customize the application parameters. With PrivateGPT, you can deploy RAG application within several clicks relying on previously deployed PostgreSQL for data storage, Apolo storage for binary files store, vLLM for LLM inference and Text Embeddings or vLLM for embeddings creation.

In this example, we will integrate with previously deployed applications. The easiest way is to build the configuration from Apolo web console and then save it to the application configuration file. You can do it with this button (after specifying all the parameters):

<figure><img src="../../../../.gitbook/assets/image (38).png" alt=""><figcaption><p>Extract PrivateGPT app configuration file</p></figcaption></figure>

\
Below is an example configuration file that deploys a PrivateGPT app

```yaml
# Example of private-gpt-v25.5.1-config.yaml

template_name: private-gpt
template_version: v25.5.1
display_name: ysem-docs-pgpt
input:
    ingress_http:
        auth: true
    embeddings_api:
        type: app-instance-ref
        instance_id: 26914c96-a284-46c3-a3db-306fa89a4a4a
        path: embeddings_internal_api
    llm_chat_api:
        type: app-instance-ref
        instance_id: 9a5b6eef-1094-40cb-ba66-4a749e2c92d4
        path: chat_internal_api
    private_gpt_specific:
        llm_temperature: 0.1
        embeddings_dimension: 768
        llm_max_new_tokens: 5000
        llm_context_window: 8192
        llm_tokenizer_name: Qwen/Qwen2-7B-Instruct
    pgvector_user:
        type: app-instance-ref
        instance_id: cfd2ebe0-c6a5-4a86-a5b0-ebc0a9834ae0
        path: postgres_users/users/0
    preset:
        name: cpu-medium
```

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f private-gpt-v25.5.1-config.yaml
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
* [Apolo web console PrivateGPT application management](../../../../apolo-console/apps/installable-apps/available-apps/privategpt.md)
* [PrivateGPT application source code](https://github.com/neuro-inc/private-gpt)
* [PostgreSQL application management](../../../../apolo-console/apps/installable-apps/available-apps/postgre-sql.md)
* [Text Embeddings application management](../../../../apolo-console/apps/installable-apps/available-apps/text-embeddings-inference.md)
* [vLLM application management](../../../../apolo-console/apps/installable-apps/available-apps/llm-inference/)
