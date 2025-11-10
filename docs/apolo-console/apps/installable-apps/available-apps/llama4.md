# LLaMA4

**LLaMA4** is a high-performance and scalable family of large language models developed by Meta AI. It is optimized for inference efficiency and supports both instruction-tuned and base variants, making it suitable for a wide range of production-level NLP tasks and deployments.

**LLaMA 4 is part of the vLLM application and is designed for instant deployment, minimizing configuration effort.**\
If you need to customize the deployment, please use the configurable application variant. Examples of tunable parameters include `server-extra-args`, Ingress authentication, and more.

#### Key Features

* **Minimal Setup**: Automatic detection of available preset based on chosen model.
* **High Throughput Inference**: Novel GPU KV caching enables faster token generation compared to traditional implementations.
* **Multi-GPU Support**: Scales seamlessly to multiple GPUs, including AMD (MI200s, MI300s) and NVIDIA (V100/H100/A100) resource pools.
* **Easy Model Downloading**: Built-in integration with Hugging Face model repositories.
* **Lightweight & Extensible**: Minimal overhead for deployment and easy to integrate with existing MLOps or monitoring solutions.

***

#### Installation and Deployment on Apolo

You can deploy Llama4 on the Apolo platform using the **`LLAMA4`** app. Apolo automates resource allocation, persistent storage, ingress, GPU detection, and environment variable injection, so you can focus on model configuration.

**Highlights of the Apolo Installation Flow**:

1. **Integration with Hugging Face**: You can pass your Hugging Face token via an environment variable to pull private models.
2. **Preset Auto-Configuration**: Preset will be chosen by default, according to HuggingFace model minimal vRAM requirements.
3. **Ingress Setup**: By default will be enabled without authentication.
4. **Autoscaling**: Apolo provides built-in support for horizontal pod autoscaling of LLaMA 4 deployments based on incoming request load. Parameters for autoscaling (min replicas - 1, max replicas - 5, 100 requests per second to start scaling)
5.  **Cache**: The caching of your model is enabled by default, the storage path is&#x20;

    ```
    storage://{cluster_name}/{org_name}/{project_name}/llm_bundles
    ```

***



## Apolo Deployment

| **Hugging Face Token** | **Required**. Provide a Hugging Face token if model is gated.  |
| ---------------------- | -------------------------------------------------------------- |
| **Model**              | **Required**. Select model size from the dropdown.             |

***

### Web Console UI

Step1 - Select  [HuggingFace](https://huggingface.co/) token

Step2 - Choose the size of your model

<figure><img src="../../../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

If Model is [gated](https://huggingface.co/docs/hub/en/models-gated), please make sure that your HuggingFace token has an access to it.

Step 3 - Install and wait for the application to be deployed. Once installed, you can find the API endpoint URL in the Outputs section of the app details page.



### Autoscaling

If you Enable the autoscaling, the following parameters will be applied:

**Default Autoscaling Configuration:**

* **Trigger Threshold**: 100 requests per second
* **Replica Range**: Minimum 1, Maximum 5 replicas
* **Behavior**: The system monitors request volume and automatically adjusts the number of running replicas to match current demand, ensuring efficient use of GPU resources while maintaining performance.

### Usage

```python
import requests

API_URL = "<APP_HOST>/v1/chat/completions"

headers = {
    "Content-Type": "application/json",
}

data = {
    "model": "meta-llama/Llama-4-Scout-17B-16E",  # Must match the model name loaded by vLLM
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

### References

* [vLLM Official GitHub Repo](https://github.com/vllm-project/vllm)
* [LLama4 Official Page](https://www.llama.com/models/llama-4/)
* [vLLM application Helm Chart Repository](https://github.com/neuro-inc/app-llm-inference)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
* [Hugging Face Model Hub](https://huggingface.co/) (for discovering or hosting models)
* [Apolo Hugging Face application management](hugging-face.md)
* [LLaMA4 install via Apolo cli](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/llama4.md)
* [Llama4 collection HuggingFace](https://huggingface.co/collections/meta-llama/llama-4-67f0c30d9fe03840bc9d0164)
* [Managing Apps](../managing-apps.md)
