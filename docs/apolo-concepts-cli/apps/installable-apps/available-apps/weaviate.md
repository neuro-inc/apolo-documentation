# Weaviate

**Weaviate** is a robust, open-source vector database that allows you to store and query data based on its semantics. It supports various modules for text, image, and multimodal vectorization, enabling semantic search, advanced filtering, and question-answering. Weaviate offers flexible deployment options and integrates seamlessly with popular machine learning models and frameworks, providing **GraphQL**, and **REST** for easy integration with your applications.

The Apolo **Weaviate App**  delivers a fully‑managed cluster with:

* REST, GraphQL & gRPC APIs
* Persistent volume for data
* Optional S3 backups
* One‑click HTTPS ingress secured by basic‑auth

For more information about Weavite, please refer to the main [Weaviate](../../../../apolo-console/apps/installable-apps/available-apps/weaviate.md) page.

## Installing

Below are the detailed instructions for installing Weaviate using Apolo CLI. For instructions on how to install it using Apolo Console, visit the main [Weaviate](../../../../apolo-console/apps/installable-apps/available-apps/weaviate.md) page.

### Installing via Apolo CLI

### Apolo CLI

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get weaviate > myweaviate.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Example of myweaviate.yaml

template_name: "weaviate"
template_version: "apolo"
input:
  preset:
    name: "cpu-small"
  persistence:
    size: 64            # GiB PVC
    enable_backups: true
  ingress_http:
    auth: false         # public (no basic‑auth)
```

Explanation of configuration parameters:

1. `preset.name` — picks CPU/RAM preset.
2. `persistence.size` — volume size; backups on by default.
3. `ingress_http.auth=false` — exposes endpoints openly; set `true` for private mode.

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f myweaviate.yaml
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

For instructions on how to access the application, please refer to the [Usage](weaviate.md#usage) section.

**Explanation**

*

***

### Inputs / Outputs _(schema v1)_

**Inputs**

<table data-header-hidden><thead><tr><th width="333.26953125"></th><th width="136.85546875"></th><th></th></tr></thead><tbody><tr><td>JSON Path</td><td>Default</td><td>Description</td></tr><tr><td><code>preset.name</code></td><td>–</td><td>Resource preset per replica.</td></tr><tr><td><code>persistence.size</code></td><td><code>32</code></td><td>Volume size (GiB).</td></tr><tr><td><code>persistence.enable_backups</code></td><td><code>true</code></td><td>Nightly S3 snapshots.</td></tr><tr><td><code>ingress_http.auth</code></td><td><code>true</code></td><td>Require basic‑auth.</td></tr></tbody></table>

**Outputs**

| Key                               | Purpose                         |
| --------------------------------- | ------------------------------- |
| `external_graphql_endpoint.*`     | Public `/v1/graphql` endpoint.  |
| `external_rest_endpoint.*`        | Public `/v1` REST endpoint.     |
| `internal_graphql_endpoint.*`     | Private `/v1/graphql` endpoint. |
| `internal_rest_endpoint.*`        | Private `/v1` REST endpoint.    |
| `internal_grpc_endpoint.*`        | Private gRPC in‑cluster.        |
| `auth.username` / `auth.password` | Basic‑auth creds (if enabled).  |

***

### Usage

Quick connectivity test & schema bootstrap:

```python
import weaviate

client = weaviate.Client(
    url="https://apolo‑taddeus‑weaviate-0aad31aa.apps.apolo.us",
    additional_headers={"Authorization": "Basic <base64‑admin‑password>"}
)

assert client.is_ready(), "Weaviate not ready"
print("Connected!")
```



#### **References**

* [Weaviate Official Page](https://weaviate.io/)
* [Weaviate Helm Chart Documentation](https://weaviate.io/developers/weaviate/installation/kubernetes)
* [Weaviate Helm Chart Repository](https://github.com/neuro-inc/weaviate-helm)
* [Weaviate Platform Documentation](https://weaviate.io/developers/weaviate)
* [Weaviate Oficial Repository](https://github.com/weaviate/weaviate)
