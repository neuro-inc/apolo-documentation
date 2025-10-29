# vLLM Inference

### Overview <a href="#overview" id="overview"></a>

[vLLM](https://github.com/vllm-project/vllm) is a high-performance and memory-efficient inference engine for large language models. It uses a novel GPU KV cache management strategy to serve transformer-based models at scale, supporting multiple GPUs (including NVIDIA and AMD) with ease. vLLM enables fast decoding and efficient memory utilization, making it suitable for production-level deployments of large LLMs.

### Managing application via Apolo CLI <a href="#managing-application-via-apolo-cli" id="managing-application-via-apolo-cli"></a>

vLLM can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing using Apolo CLI.

**Install via Apolo CLI**

**Step 1** — Use the CLI command to get the application configuration file template:

```
apolo app-template get llm-inference > llm.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Application template configuration for: llm-inference
# Fill in the values below to configure your application.
# To use values from another app, use the following format:
# my_param:
#   type: "app-instance-ref"
#   instance_id: "<app-instance-id>"
#   path: "<path-from-get-values-response>"

template_name: llm-inference
template_version: v25.7.0
input:
  # Select the resource preset used per service replica.
  preset:
    # The name of the preset.
    name: <>
  # Enable access to your application over the internet using HTTPS.
  ingress_http:
    # Enable or disable HTTP ingress.
    enabled: true
  # Hugging Face Model Configuration.
  hugging_face_model:
    # The name of the Hugging Face model.
    model_hf_name: <>
    # The Hugging Face API token.
    hf_token: <>


```

Explanation of configuration parameters:

1. **Resource Preset**: Choose a compute preset to define the hardware resources allocated. Example: `H100x1`
2. **HTTP Authentication**: Enabled for secure access.
3. **HuggingFace Model:** Set HuggingFace model that you want to deploy.

**Step 3** — Deploy the application in your Apolo project:

```
apolo app install -f llm.yaml
```

Monitor the application status using:

```
apolo app list
```

To uninstall the application, use:

```
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](vllm-inference.md#usage) section.

### Usage

After installation, you can utilize vLLM for different kind of workflows:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **vLLM** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details" page, scroll down to the Outputs sections. To launch the applications, find the **HTTP API** output with with the public domain address, copy and open it and paste to the script.

```python
import requests

API_URL = "<APP_HOST>/v1/chat/completions"

headers = {
    "Content-Type": "application/json",
}

data = {
    "model": "meta-llama/Llama-3.1-8B-Instruct",  # Must match the model name loaded by vLLM
    "messages": [
        {
            "role": "system",
            "content": "You are a helpful assistant that gives concise and clear answers.",
        },
        {
            "role": "user",
            "content": (
                "I'm preparing a presentation for non-technical stakeholders "
                "about the benefits and limitations of using large language models in our customer support workflows. "
                "Can you help me outline the key points I should include, with clear, jargon-free explanations and practical examples?"
            ),
        },
    ]
}


if __name__ == '__main__':

    response = requests.post(API_URL, headers=headers, json=data)
    response.raise_for_status()

    reply = response.text
    status_code = response.status_code
    print("Assistant:", reply)
    print("Status Code:", status_code)
```

### References:

* [vLLM Official GitHub Repo](https://github.com/vllm-project/vllm)
* [app-llm-inference Helm Chart Repository](https://github.com/neuro-inc/app-llm-inference)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
* [Hugging Face Model Hub](https://huggingface.co/) (for discovering or hosting models)
* [LLM inference install via UI](../../../../apolo-console/apps/installable-apps/available-apps/llm-inference/)
* [Managing Apps](../managing-apps.md)
