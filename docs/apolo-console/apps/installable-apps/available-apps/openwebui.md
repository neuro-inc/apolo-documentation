# OpenWebUI

### Overview

OpenWebUI is an extensible, feature-rich, and user-friendly self-hosted web interface for interacting with Large Language Models (LLMs). It is designed to operate entirely independent of AI providers such as OpenAI' ChatGPT or Google Gemini, providing a private and secure environment for your AI interactions. OpenWebUI supports various LLM runners, including vLLM, Ollama and other OpenAI API-compatible tools, making it a versatile solution for deploying and managing language models. With an interface similar to ChatGPT, it offers a familiar and intuitive user experience.

This application is ideal for users who want to run language models locally or in a self-hosted environment, ensuring data privacy and control. Its extensive customization options allow developers and organizations to create unique AI solutions tailored to their specific needs.

### Key Features

* **Resource Presets:** Optimize performance by choosing from predefined resource configurations based on your workload needs.
* **Broad LLM Support:** Natively integrates with the Apolo's LLM Inference server and other OpenAI API compatible endpoints, allowing the use of a wide range of models, including Llama, Mistral, and Claude.
* **Local Operation:** Designed to run entirely within the cluster, ensuring that your data remains private and is not used for model training.
* **Retrieval Augmented Generation (RAG):** Features a built-in RAG system that allows you to "chat" with your documents by uploading them and using a tagging system to organize and search within your knowledge base.
* **Web Search Integration:** Enhance your LLM's knowledge with real-time information from the web through integrations with Google, Brave Search, and other providers.
* **Image Generation:** Utilize models with image generation capabilities to create images directly from your conversations.
* **Customization and Extensibility:** Offers extensive options to customize the interface, integrate with various APIs, and modify functionalities to suit your needs. This includes adding new AI models, creating custom buttons, and implementing content filters.
* **Multi-Model Comparison:** Compare responses from different models side-by-side to evaluate their performance on the same prompt.
* **Responsive Design:** Access OpenWebUI from your desktop, laptop, or mobile device with a seamless user experience. It is also available as a Progressive Web App (PWA) for a native app-like experience on mobile.

### Installing

OpenWebUI can be installed on Apolo either via the [CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/openwebui.md) or the Web Console. Below are the detailed instructions for installing OpenWebUI using Apolo Console.

#### Install via Apolo Web Console

**Step 1** — Navigate to the Apps page, find OpenWebUI from the list and click the corresponding "Install" button. This will redirect you to the installation page.

<figure><img src="../../../../.gitbook/assets/Screenshot From 2025-07-21 14-07-54.png" alt=""><figcaption></figcaption></figure>

**Step 2** — Configure the application by filling the required fields and click install.

<figure><img src="../../../../.gitbook/assets/Screenshot From 2025-07-21 14-08-41.png" alt=""><figcaption></figcaption></figure>

The following installation options are available:

* **Resource Preset:** Choose from the dropdown menu, usually a small with 1 or 2 vCPUs should be enough - this is the only required field that you must set.
* **OpenWebUI App Configuration:**
  * **Postgres User Credentials:** Select the credentials for the PostgreSQL database that will be used for both the chat history and the vector embeddings. Refer to [Potstgres app](postgre-sql.md) documentation for instructions on how to install it.
  * **OpenAI Compatible Embeddings API:** Choose the API for text embeddings. Refer to [Text Embeddings app](text-embeddings-inference.md) documentation for instructions on how to install it.
  * **OpenAI Compatible Chat API:** Select the chat API to be used. Refer to [vLLM app](llm-inference/) documentation for instructions on how to install it.
* **Networking Settings**
  * **Enable HTTP Ingress:** This option is enabled by default, so your application is only accessible to authenticated users in Apolo with permissions in the current project.
* **Metadata:** Enter the application display name.

**Step 3** — Click "Install" to initiate deployment. You will be redirected to the application details page where you can monitor the installation progress and view application outputs. For instructions on how to access the application, please refer to the Usage section.

### Usage

After installation, you can utilize OpenWebUI for various AI tasks and access it directly from your browser.

To view and manage installed instances of the OpenWebUI app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the OpenWebUI app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

<figure><img src="../../../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

Once in the "Details" page, scroll down to the **Outputs** sections. To launch the application, find the **HTTP API** output with the public domain address, copy and open it in a dedicated browser window.

<figure><img src="../../../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

You can now access OpenWebUI to run experiments, interact with models, and collaborate with colleagues.

<figure><img src="../../../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

### Video: Installing and Using OpenWebUI

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FUD8kiAsnN8MKP7nzsJRQ%2Fuploads%2F8RFFDPchubdySzZGjOpk%2Fdemo.mp4?alt=media&token=d4f0367c-28c9-4f93-b53f-eb3b0347ec2a" %}

### Example Workflow

1. **Start an OpenWebUI Session:** Access the OpenWebUI through the provided URL.
2. **Document Interaction:** Upload documents to create a knowledge base and use the RAG feature to ask questions about the content.
3. **Model Interaction:** Engage in conversations with various large language models and compare their responses.
4. **Collaboration:** Share your findings and collaborate with team members within the Apolo platform.

### References

* [OpenWebUI documentation](https://docs.openwebui.com/)
* [Installing OpenWebUI using Apolo CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/openwebui.md)
