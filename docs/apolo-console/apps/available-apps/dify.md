# Dify

Dify is an open-source development platform designed for building, managing, and deploying applications powered by large language models (LLMs). Dify provides an intuitive interface that combines essential features such as AI workflow, Retrieval-Augmented Generation (RAG) pipelines, agent-based capabilities, model management, and observability tools, all of which help users transition quickly from prototype to production.

### Key Features

* **AI Workflow Management:** Streamlines the end-to-end development process for LLM apps.
* **RAG Pipelines and Agent Capabilities:** Supports Retrieval-Augmented Generation and agent functionality to handle complex data queries and automate tasks.
* **Model Management:** Enables efficient model management for various LLM deployments.
* **Observability:** Provides tools to monitor and improve app performance in real time.

### Installation and Deployment

You can deploy Dify using the Apolo, which facilitates Helm chart deployment and integrates with other applications running on Apolo, such as Postgres clusters, for seamless data handling.

The installation process automates:

1. Integration with existing PGVector app
2. Dify configs to persist datasets objects at [Apolo Buckets](https://github.com/neuro-inc/apolo-documentation/blob/master/docs/apolo-console/apps/pre-installed/buckets.md)
3. Web interface ingress configuration

#### Parameter Descriptions

| Parameter                          | Type    | Description                                                                                                      |
| ---------------------------------- | ------- | ---------------------------------------------------------------------------------------------------------------- |
| `api.replicas`                     | Integer | Number of API service replicas (default: 1).                                                                     |
| `api.preset_name`                  | String  | Required. CPU/memory preset for the API service (e.g., `cpu-small`).                                             |
| `worker.replicas`                  | Integer | Number of worker service replicas (default: 1).                                                                  |
| `worker.preset_name`               | String  | Required. CPU/memory preset for worker service (e.g., `cpu-small`).                                              |
| `proxy.preset_name`                | String  | Required. CPU/memory preset for the proxy service.                                                               |
| `web.replicas`                     | Integer | Number of web service replicas (default: 1).                                                                     |
| `web.preset_name`                  | String  | Required. CPU/memory preset for the web service.                                                                 |
| `redis.master.preset_name`         | String  | Required. CPU/memory preset for Redis master instance.                                                           |
| `externalPostgres.platformAppName` | String  | Required. PGVector app name to integrate with the Dify app.                                                      |
| `externalPostgres.username`        | String  | Optional. Username within the PGVector app (first available user used if not specified).                         |
| `externalPostgres.dbName`          | String  | Optional. Database to use within the PGVector app (first available DB used if not specified).                    |
| `externalPgvector.platformAppName` | String  | Required. PGVector app name for direct vector data integration. Note: it could be the same externalPostgres app. |
| `externalPgvector.username`        | String  | Optional. Username for PGVector integration (first available user used if not specified).                        |
| `externalPgvector.dbName`          | String  | Optional. Database to use within PGVector for vector data storage.                                               |

This setup uses Helm chart for flexible and scalable deployment, enabling seamless integration with PostgreSQL and PGVector services. Adjust parameters based on your infrastructure requirements and previously installed Postgres app names.

After the installations, users could also integrate LLM inference and embeddings apps to this app. This part of documentation is under development

### References

* [Dify helm chart documentation](https://github.com/neuro-inc/dify-helm)
* [Dify platform documentation](https://docs.dify.ai/)
