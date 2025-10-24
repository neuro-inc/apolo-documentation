# DeepSeekR1

### Overview <a href="#overview" id="overview"></a>

**DeepSeekR1 is a high-performance and scalable family of large language models developed by DeepSeek AI.**\
It is optimized for inference efficiency and supports both base and instruction-tuned variants, making it suitable for a wide range of production-grade NLP workloads such as coding, reasoning, and chat.

**DeepSeek is available through the vLLM application and is designed for instant deployment, reducing the need for manual configuration.**\
If you need to customize the deployment, use the configurable application variant. Examples of tunable parameters include `server-extra-args`, Ingress authentication, and more.

### Managing application via Apolo CLI <a href="#managing-application-via-apolo-cli" id="managing-application-via-apolo-cli"></a>

DeepSeekR1 can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing using Apolo CLI.

**Install via Apolo CLI**

**Step 1** — Use the CLI command to get the application configuration file template:

```
apolo app-template get deepseek-inference > deepseek.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Application template configuration for: llama4-inference
# Application template configuration for: deepseek-inference
# Fill in the values below to configure your application.
# To use values from another app, use the following format:
# my_param:
#   type: "app-instance-ref"
#   instance_id: "<app-instance-id>"
#   path: "<path-from-get-values-response>"

template_name: deepseek-inference
template_version: v25.7.1
input:
  # Apolo Secret Configuration.
  hf_token:
    key: ''
  # Enable or disable autoscaling for the LLM.
  autoscaling_enabled: false
  size: R1
  llm_class: deepseek_r1


```

Explanation of configuration parameters:

1. **HuggingFace token:** Set HuggingFace model that you want to deploy.
2. **Autoscaling:** enable if needed true/false
3. **Size:** One of the available model sizes

**Step 3** — Deploy the application in your Apolo project:

```
apolo app install -f deepseek.yaml
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

For instructions on how to access the application, please refer to the [Usage](deepseekr1.md#usage) section.

### Usage

After installation, you can utilize **DeepSeek** for different kind of workflows:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **DeepSeek** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details" page, scroll down to the Outputs sections. To launch the applications, find the **HTTP API** output with with the public domain address, copy and open it and paste to the script.

```python
import requests

API_URL = "<APP_HOST>/v1/chat/completions"

headers = {
    "Content-Type": "application/json",
}

data = {
    "model": "deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B",  # Must match the model name loaded by vLLM
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
* [Managing Apps](../managing-apps.md)
* [Apolo Hugging Face application management](../../../../apolo-console/apps/installable-apps/available-apps/hugging-face.md)
* [DeepSeekR1 installation via Apolo UI](../../../../apolo-console/apps/installable-apps/available-apps/deepseekr1.md)
* [vLLM inference install via CLI](vllm-inference.md)
* [DeepSeek HuggingFace collection](https://huggingface.co/deepseek-ai/DeepSeek-R1)

