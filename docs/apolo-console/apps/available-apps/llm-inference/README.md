# LLM Inference

[vLLM](https://github.com/vllm-project/vllm) is a high-performance and memory-efficient inference engine for large language models. It uses a novel GPU KV cache management strategy to serve transformer-based models at scale, supporting multiple GPUs (including NVIDIA and AMD) with ease. vLLM enables fast decoding and efficient memory utilization, making it suitable for production-level deployments of large LLMs.

#### Key Features

* **High Throughput Inference**: Novel GPU KV caching enables faster token generation compared to traditional implementations.
* **Multi-GPU Support**: Scales seamlessly to multiple GPUs, including AMD (MI200s, MI300s) and NVIDIA (V100/H100/A100) resource pools.
* **Easy Model Downloading**: Built-in integration with Hugging Face model repositories.
* **Flexible Configuration**: Control precision (`--dtype`), context window size, parallelism (`tensor-parallel-size`, `pipeline-parallel-size`), etc.
* **Lightweight & Extensible**: Minimal overhead for deployment and easy to integrate with existing MLOps or monitoring solutions.

#### Installation and Deployment on Apolo

You can deploy vLLM on the Apolo platform using the **`vLLM`** app. Apolo automates resource allocation, persistent storage, ingress, GPU detection, and environment variable injection, so you can focus on model configuration.

**Highlights of the Apolo Installation Flow**:

1. **Resource Allocation**: Choose an Apolo preset (e.g. `gpu-xlarge`, `mi210x2`) that specifies CPU, memory, and GPU resources.
2. **GPU Auto-Configuration**: If your preset includes multiple GPUs, environment variables (e.g. `CUDA_VISIBLE_DEVICES` or `HIP_VISIBLE_DEVICES`) are automatically set, along with a sensible default for parallelization.
3. **Ingress Setup**: Enable an ingress to expose vLLM’s HTTP endpoint for external access.
4. **Integration with Hugging Face**: You can pass your Hugging Face token via an environment variable to pull private models.



***

## Apolo Deployment

#### Parameter Descriptions

The following parameters can be set with Apolo’s CLI (`apolo run --pass-config ... install ... --set <key>=<value>`). Many are optional but can be used to customize your deployment:

| **Resource Preset**             | Required. Apolo preset for resources. E.g. **`gpu-xlarge`**, `H100X1`, `mi210x2`. Sets CPU, memory, GPU count, and GPU provider.                                                            |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Hugging Face Model**          | Required. Provide a Model Name in specified field. And Higging Face token if model is gated. E.g.  **`sentence-transformers/all-mpnet-base-v2`**                                            |
| **Enable HTTP Ingress**         | Exposes an application externally over HTTPS                                                                                                                                                |
| **Hugging Face Tokenizer Name** | Name or path of the huggingface tokenizer to use. If unspecified, model name or path will be used.                                                                                          |
| **Server Extra Args**           | Optional. Specify additional args for llm. See [https://docs.vllm.ai/en/v0.5.1/models/engine\_args.html](https://docs.vllm.ai/en/v0.5.1/models/engine_args.html)                            |
| **Cache Config**                | Optional. Configure storage cache path, used to persist your model. Important for Autoscaling purposes. If not specified, PV will be created automatically and attached to the application. |

Any additional chart values can also be provided through `--set` flags, but the above are the most common.

***

### Web Console UI

Step1 - Select the Preset you want to use (Currently only GPU-accelerated presets are supported)

Step2 - Select Model from [HuggingFace](https://huggingface.co/) repositories

<figure><img src="../../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p>Part 1 - vLLM app deployment</p></figcaption></figure>

If Model is [gated](https://huggingface.co/docs/hub/en/models-gated), please provide the HuggingFace token, as a string of Apolo Secret.

Step 3 - Install and wait for the outputs, at the Outputs section of an app

<figure><img src="../../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

* `preset.name=gpu-l4-x1` requests 1 GPUs (AMD MI210). Apolo automatically sets `HIP_VISIBLE_DEVICES=0,1` , `ROCR_VISIBLE_DEVICES=0,1`  and default parallelization flags unless overridden.
* `model_hf_name: "meta-llama/Llama-3.1-8B-Instruct"`: The Hugging Face model to load.
* `ingress_http`: Creates a public domain (e.g. `vllm-large.apps.<YOUR_CLUSTER_NAME>.org.neu.ro`) pointing to the vLLM deployment.



### Usage

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

### References

* [vLLM Official GitHub Repo](https://github.com/vllm-project/vllm)
* [app-llm-inference Helm Chart Repository](https://github.com/neuro-inc/app-llm-inference)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
* [Hugging Face Model Hub](https://huggingface.co/) (for discovering or hosting models)

