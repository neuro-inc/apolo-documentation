# Text Embeddings Inference

The [Text Embeddings App](https://github.com/huggingface/text-embeddings-inference) transforms raw text into dense, high-dimensional vectors using state-of-the-art embedding models such as BERT, RoBERTa, or other models. These embeddings capture semantic meaning and can be used as input for downstream ML tasks or stored in vector databases.

### Supported Models

Text Embeddings Inference currently supports Nomic, BERT, CamemBERT, XLM-RoBERTa models with absolute positions, JinaBERT model with Alibi positions and Mistral, Alibaba GTE, Qwen2 models with Rope positions, MPNet, and ModernBERT.

More detailed description can be found in [Github Repo](https://github.com/huggingface/text-embeddings-inference)

### Key Features

* No model graph compilation step
* Metal support for local execution on Macs
* Small docker images and fast boot times. Get ready for true serverless!
* Token based dynamic batching
* Optimized transformers code for inference using [Flash Attention](https://github.com/HazyResearch/flash-attention), [Candle](https://github.com/huggingface/candle) and [cuBLASLt](https://docs.nvidia.com/cuda/cublas/#using-the-cublaslt-api)
* [Safetensors](https://github.com/huggingface/safetensors) weight loading
* [ONNX](https://github.com/onnx/onnx) weight loading
* Production ready (distributed tracing with Open Telemetry, Prometheus metrics)

## Apolo deployment

| Field                   | Description                                                                                                                                     |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| **Resource Preset**     | Required. Apolo preset for resources. E.g. **`gpu-xlarge`**, `H100X1`, `mi210x2`. Sets CPU, memory, GPU count, and GPU provider.                |
| **Hugging Face Model**  | Required. Provide a Model Name in specified field. And Higging Face token if model is gated. E.g. **`sentence-transformers/all-mpnet-base-v2`** |
| **Enable HTTP Ingress** | Exposes an application externally over HTTPS                                                                                                    |

### Web Console UI

Step1 - Select the Preset you want to use (Currently only GPU-accelerated presets are supported)

Step2 - Select Model from [HuggingFace](https://huggingface.co/) repositories

<figure><img src="../../../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption><p>Part 1 Text Embeddings Inference installation process</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption><p>Part 2 Text Embeddings Inference installation process</p></figcaption></figure>

If Model is [gated](https://huggingface.co/docs/hub/en/models-gated), please provide the HuggingFace token, as a string of Apolo Secret.

Step3 - Install and wait for the application to be deployed. Once installed, you can find the API endpoint URL in the Outputs section of the app details page.

<figure><img src="../../../../.gitbook/assets/image (5) (1) (1) (1) (1).png" alt=""><figcaption><p>Application details</p></figcaption></figure>

## Usage

```python
import requests
import json

# URL of your TEI server (adjust if running locally or behind a proxy)
TEI_ENDPOINT = "https://<YOUR_OUTPUTS_ENDPOINT>"

# Example texts to embed
texts = [
    "The quick brown fox jumps over the lazy dog.",
    "Artificial intelligence is transforming the world."
]

# Request payload
payload = {
    "inputs": texts,
    "normalize": True  # Optional: normalize vectors to unit length
}

if __name__ == '__main__':

    # Make the request
    response = requests.post(
        TEI_ENDPOINT,
        headers={"Content-Type": "application/json"},
        data=json.dumps(payload)
    )

    # Check for errors
    if response.status_code != 200:
        print(f"Error {response.status_code}: {response.text}")
        exit(1)

    # Parse and print the embeddings
    embeddings = response.json()
    for i, embedding in enumerate(embeddings):
        print(f"Text: {texts[i]}")
        print(f"Embedding: {embedding}")
        print()
```

### References

* [Hugging Face Text Embeddings Inference GitHub repo](https://github.com/huggingface/text-embeddings-inference)
* [Hugging Face platform](https://huggingface.co/)
* [Apolo Hugging Face application management](hugging-face.md)
* [Managing Apps](../managing-apps.md)
* [Text Embedding Inference CLI installation](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/text-embeddings-inference.md)
