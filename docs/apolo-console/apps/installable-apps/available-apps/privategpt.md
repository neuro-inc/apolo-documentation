# PrivateGPT

## Overview

**PrivateGPT** is a secure, privacy-first application that lets you run RAG-powered GPT-style chat over your own documents in a fully private environment. Built on the popular open-source project by Zylon, it uses LLM embeddings and FastAPI-compatible APIs to enable:

* **Document ingestion**: automatically parse, split, embed, and index your files.
* **Contextual Q\&A and chat**: query ingested documents with LLM-powered responses that use retrieved context.
* **Low‑level pipeline tools**: generate embeddings, fetch top-relevant chunks—ideal for custom AI workflows.

#### Why use PrivateGPT?

* **Fortified privacy**: Data never leaves your Apolo environment — no external API calls.
* **Easy integration**: Follows the OpenAI API standard, making it easy to swap into existing tools.
* **Flexible pipelines**: Use high-level chat/completion APIs or low-level embedding and retrieval endpoints for custom RAG workflows.
* **Developer friendly**: Includes scripts for ingestion, model management, and even a Gradio UI for fast prototyping (checkout application repository).

### Integrations

On the Apolo platform, **PrivateGPT** might be integrate enhanced with:

1. **LLM inference** application (streaming & non‑streaming completions)
2. **Text embeddings** application for document/context processing
3. **PostgreSQL with PGVector** application for scalable, low-latency semantic retrieval

This design allows users to upload documents or build ingestion pipelines, and then query them privately with zero data leakage — all managed by Apolo behind the scenes.

## Installing

### Installing with Apolo Console

In this guide, we presume you've already deployed [vLLM Inference](llm-inference/), [Text embeddings](text-embeddings-inference.md) and [PostgreSQL](postgre-sql.md) applications, since we are going to show the integration process with those apps. The overview of application installation process via web console could be found [here](../managing-apps.md).

Below are the detailed instructions for installing PrivateGPT using Apolo Console. For instructions on how to install it using Apolo CLI, visit the [dedicated page](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/privategpt.md).&#x20;

{% stepper %}
{% step %}
**Select "PrivateGPT" application at Apolo web console**

Access the Apolo web console and go to the "Apps" section. We presume you are already authorized in web console and a participant of organization and project.
{% endstep %}

{% step %}
### Configure application

**Resource Preset:** Here you select an appropriate preset that specifies CPU, memory. This application under the hood serves static files and coordinates communication among other application. It does not perform compute by itself, so the preset with 1 vCPU and  2 GiB of RAM should be sufficient to serve your needs.

For our use-case we use `cpu-medium` with exactly those numbers.

**HTTP Access**: allows you to enable or disable Apolo-powered HTTP authorization for the application API and web UI public domain. We enable it for security.

**PostgreSQL integration** is required for PrivateGPT in order to store documents, their embeddings and metadata for later retrieval in chat. In the opened window select PostgreSQL credentials from previously installed in Apolo PostgreSQL application.

When integrating with PostgreSQL app, make sure you are not using `postgres` user, this will not work due to security reasons.

You can also configure access to the PostgreSQL instance managed by you, among requirements: user should be able to connect, create tables in own or public schema for the specified database. PGVector add-on should also be pre-installed in the database.

**Embeddings API integration** is required for PrivateGPT to process user documents and queries. Under the hood PrivateGPT does not serve embedding model itself. Instead, it relies on access to generic OpenAI API-compatible Embeddings endpoint to process the document and return embedding values. Here we also rely on provided by Apolo integration with Text Embeddings application deployed beforehand.&#x20;

**LLM Chat API integration** is required for PrivateGPT to serve user queries. PrivateGPT does not serve LLM itself too. Instead, it relies on usage of OpenAI-compatible Chat endpoint. Here we also rely on provided by Apolo integration with vLLM application deployed beforehand, therefore, select the corresponding integration for those fields.

As with PostgreSQL application, you can utilize your own OpenAI-compatible APIs by providing manual values for the corresponding inputs (for embeddings and chat APIs).

{% hint style="info" %}
When integrating apps with HTTP web services, such as vLLM or Text Embeddings, make sure you use on internal, intra-cluster endpoints instead of public ones (that you can access from your web browser). This will reduce network traffic, round-robin latency, load-balancer and firewall loads, etc.
{% endhint %}

**LLM Temperature** is a well-known LLM inference control. It controls "creativity" of the model. The higher value, the more creative responses it generates. Since this is a RAG application, we do not hallucinations here, therefore, set it somewhat around 0.1.

**Embeddings Dimensions** controls the number of features used to represent data (like words, images, or user preferences) in a machine learning model. A higher dimension generally allows for more nuanced representation, but also increases computational cost and potential for overfitting.&#x20;

{% hint style="info" %}
Number of embeddings dimensions is defined by the embeddings model and you can not change it.

If you are using Hugging Face-provided model, you can find number embed dimensions in model description cart or within the model configuration file on Hugging Face.
{% endhint %}

**LLM Max new tokens** defines the model answer maximal length.

**LLM Context window** defines how much data PrivateGPT can fit into the model within single query. This limited by LLM model itself (you can find this number in model description cart) and by underlying hardware (resource preset you use, VRAM in particular).&#x20;

**LLM Tokenizer** defines which text tokenizer PrivateGPT should use while communicating with vLLM. We advise setting it equal to the model name in Hugging Face, but you can also leave it empty.

The last piece is the application name. We advice giving meaningful names for the resources and deployments in any case.
{% endstep %}

{% step %}
### Review configuration

Now we can review the configuration.

{% tabs %}
{% tab title="Resource preset & ingress authorization" %}
<figure><img src="../../../../.gitbook/assets/image (41).png" alt=""><figcaption><p>Preset and ingress auth</p></figcaption></figure>
{% endtab %}

{% tab title="Upstream apps integration" %}
<figure><img src="../../../../.gitbook/assets/image (42).png" alt=""><figcaption><p>PostgreSQL integration</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (43).png" alt=""><figcaption><p>Text embeddings integration</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (44).png" alt=""><figcaption><p>vLLM integration</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (45).png" alt=""><figcaption><p>Overview</p></figcaption></figure>
{% endtab %}

{% tab title="PrivateGPT internals" %}
<figure><img src="../../../../.gitbook/assets/image (48).png" alt=""><figcaption><p>PrivateGPT configuration</p></figcaption></figure>
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Triggering installation

After clicking the "install" button, you will be redirected to the application details page and Apolo will take care of application installation.

<figure><img src="../../../../.gitbook/assets/image (49).png" alt=""><figcaption><p>PrivateGPT application pending installation</p></figcaption></figure>

After few minutes, you could hit "refresh" button or wait while the web console updates status of the application. It should switch into "healthy" state. Now you could use the application.
{% endstep %}
{% endstepper %}

As for the approach of managing service deployment applications via CLI, see dedicated the instructions on a [documentation dedicated page](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/service-deployment.md).

## Usage

The application allows user to upload documents for analysis and ask questions about the documents content. There are two main ways of using it:

* using Web UI
* using API

Web UI is available in the root domain of the application. To identify this address, navigate to installed application page, make sure the installation process completed (by verifying status and it's transitions). By adding `/docs` path to this URL, you will get access to the Swagger UI with API endpoints documentation. Web UI uses those endpoints.

### Access via public domain

Within outputs section, you will see HTTP API section with the domain URL:

<figure><img src="../../../../.gitbook/assets/image (39).png" alt=""><figcaption><p>PrivateGPT outputs</p></figcaption></figure>

Open this link in your browser and you will see the web UI.&#x20;

Let's now focus on web UI usage of the application. Upload file(s) from your local machine to the PrivateGPT using "Upload files" button on the web UI. Private GPT will start processing the document. You will see the corresponding notification when the processing is done and you may start asking questions.

<figure><img src="../../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

When the user uploads document, the following actions take place:

1. Application stores the document in the persistent Apolo storage.
2. The document gets sliced into chunks of smaller text portions.
3. Application sends each chunk to embedding endpoint in order to get numeric representation of data (embeddings).
4. Afterwards, embeddings are stored in PGVector using PostgreSQL app integration

When a user asks a question to PrivateGPT, the question itself is also embedded. PrivateGPT then retrieves relevant information and sends both the user's question and the retrieved data to the LLM inference endpoint (via the integrated vLLM application).

This is a simplified description of the flow, but it should give you some insight into how RAG systems work—and how PrivateGPT operates in particular.

Let's upload some documents and ask some questions. In this case, I will upload Kubernetes cookbook and ask questions related to the LLM deployments scaling.

<figure><img src="../../../../.gitbook/assets/image (36).png" alt=""><figcaption><p>Working with PrivateGPT application</p></figcaption></figure>

### Intra-cluster access

We will not cover API usage in this particular example, but you could find the API description by appending `/docs` to the previously opened URI in your browser. This will show the available endpoints and the requests examples together with potential parameters.

<figure><img src="../../../../.gitbook/assets/image (37).png" alt=""><figcaption><p>PrivateGPT FastApi documentation</p></figcaption></figure>

See differences and gotchas description in [#intra-cluster-access](service-deployment.md#intra-cluster-access "mention") description of Service Deployment application.

## Cleanup

When the application is not needed anymore, you could remove it by clicking the "uninstall" button on the installed app details/status screen.

## References

* [Apolo CLI Application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
* [Apolo CLI Application template commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [PrivateGPT application management via CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/privategpt.md)
* [PrivateGPT application source code](https://github.com/neuro-inc/private-gpt)
* [PostgreSQL application management](postgre-sql.md)
* [Text Embeddings application management](text-embeddings-inference.md)
* [vLLM application management](llm-inference/)
