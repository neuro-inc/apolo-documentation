# Bifrost

## Overview

[Bifrost](https://docs.getbifrost.ai/overview) is an AI gateway. It puts the vLLM applications of your project and hosted model providers behind one OpenAI-compatible API, and adds:

* Virtual keys with budgets and rate limits
* Load balancing and failover between backends
* A dashboard with request logs, token usage and cost

Typical uses:

* Give several applications one endpoint for all models instead of one endpoint per model
* Hand out virtual keys to teams and cap what each of them can spend
* Put a self-hosted model and a hosted provider behind the same API

## Installing the Bifrost App

1. Open the **Apps** section of the **Apolo Console**
2. Find **Bifrost** in the list of available apps
3. Click **Install**

### 1. Resource Preset

Choose a preset with at least 512 MB of memory.

The gateway keeps a request in memory while it handles it, so the largest request it accepts depends on the preset: about 40 MB of request body per 1 GB of memory, 100 MB at most. Pick a larger preset if you send images or long documents.

### 2. Networking

Choose how the gateway is reached from outside the cluster:

* **Apolo Platform Authentication** (default) – a request needs a platform token
* **No Authentication** – the gateway is open to the internet; virtual keys must then be required, see **Security**

### 3. Storage

Choose where the gateway keeps its configuration and request logs:

* **SQLite** – a volume attached to the gateway. Set its size in GB
* **PostgreSQL** – a user of an installed [PostgreSQL](postgre-sql/) app, version 16 or newer. Pick it through **App Integration**

Storage cannot be changed after installation.

### 4. Security

* **Admin Username** and **Admin Password** – the login for the dashboard and the management API. The password is an Apolo Secret
* **Encryption Key** – an Apolo Secret with the key that encrypts stored provider keys and virtual keys. Generate it once, for example with `openssl rand -base64 32`. It cannot be changed after installation: with a different key the gateway cannot read its data and does not start
* **Require Virtual Key** – reject model requests that carry no virtual key. Leave it off if other apps of the project use the gateway through integrations. Turn it on when the ingress has no authentication

### 5. vLLM Backends

Add one entry per [vLLM](llm-inference/) app the gateway should serve:

* **vLLM API** – pick the chat API of the vLLM app through **App Integration**
* **API Key** – an Apolo Secret, only if the vLLM server was started with an API key
* **Served Model Name** – only if the server serves the model under a name other than the Hugging Face model name

### 6. External Providers

Add one entry per hosted provider account: choose the provider and an Apolo Secret with its API key. Supported providers: OpenAI, Anthropic, Gemini, Mistral, Groq, Cohere, OpenRouter, Perplexity, Cerebras, xAI, Hugging Face and Nebius.

### 7. Install

Click **Install**. When the app is healthy, its outputs show the dashboard URL and the API endpoints.

## Managing Providers

Backends and providers are managed in the app configuration, not in the dashboard. To add or remove one, open the installed app, change **vLLM Backends** or **External Providers** and save.

On every start the gateway brings its providers in line with the configuration. A provider or a provider key added in the dashboard is removed on the next restart.

Virtual keys, budgets, rate limits, teams and routing rules are managed in the dashboard and are kept.

## Using the Gateway

### Model Names

Request a vLLM model as `vllm/<model name>`, for example `vllm/meta-llama/Llama-3.1-8B-Instruct`. Request a hosted model as `<provider>/<model>`, for example `openai/gpt-4o-mini`.

Always use the `vllm/` prefix for vLLM models. A name without it is treated as `<provider>/<model>` when it starts with a provider name: `openai/gpt-oss-20b` is sent to OpenAI, `vllm/openai/gpt-oss-20b` to your vLLM app.

`GET /v1/models` lists every model the gateway serves.

### From Another App in the Project

The **Chat APIs** output holds one API per vLLM model. An app that takes an OpenAI-compatible chat API as an integration can pick it in **App Integration**.

Inside the project the gateway is reached at the internal URL from the **Gateway API** output without a platform token:

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://<internal host>:8080/v1",
    api_key="<virtual key, or any text if virtual keys are not required>",
)

response = client.chat.completions.create(
    model="vllm/meta-llama/Llama-3.1-8B-Instruct",
    messages=[{"role": "user", "content": "Hello"}],
)
print(response.choices[0].message.content)
```

### From Outside the Cluster

With platform authentication on the ingress, send the platform token in `Authorization` and the virtual key, if required, in `x-bf-vk`:

```bash
curl https://<external host>/v1/chat/completions \
  -H "Authorization: Bearer <platform token>" \
  -H "x-bf-vk: <virtual key>" \
  -H "Content-Type: application/json" \
  -d '{"model": "vllm/meta-llama/Llama-3.1-8B-Instruct", "messages": [{"role": "user", "content": "Hello"}]}'
```

With no authentication on the ingress, the virtual key goes in `Authorization` like an OpenAI API key:

```bash
curl https://<external host>/v1/chat/completions \
  -H "Authorization: Bearer <virtual key>" \
  -H "Content-Type: application/json" \
  -d '{"model": "vllm/meta-llama/Llama-3.1-8B-Instruct", "messages": [{"role": "user", "content": "Hello"}]}'
```

### Virtual Keys

1. Open the dashboard from the app outputs and sign in with the admin login
2. Create a virtual key in the **Governance** section
3. Allow the providers, models and provider keys the virtual key may use. A key with nothing allowed is accepted and then fails every request
4. Optionally attach a budget and a rate limit

## Limits

* The gateway runs as a single instance
* The management API accepts the admin login only. Through an ingress with platform authentication it works from a browser session, not with a platform token
* A vLLM backend in another project is not reachable by its internal URL

## References

* [Bifrost documentation](https://docs.getbifrost.ai/overview)
* [Bifrost on GitHub](https://github.com/maximhq/bifrost)
