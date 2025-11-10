# Text Embeddings Inference

### Overview <a href="#overview" id="overview"></a>

The [Text Embeddings App](https://github.com/huggingface/text-embeddings-inference) transforms raw text into dense, high-dimensional vectors using state-of-the-art embedding models such as BERT, RoBERTa, or other models. These embeddings capture semantic meaning and can be used as input for downstream ML tasks or stored in vector databases.

### Managing application via Apolo CLI <a href="#managing-application-via-apolo-cli" id="managing-application-via-apolo-cli"></a>

Shell can be installed on Apolo either via the CLI or the Web Console. Below are the detailed instructions for installing using Apolo CLI.

**Install via Apolo CLI**

**Step 1** — Use the CLI command to get the application configuration file template:

```
apolo app-template get text-embeddings-inference > tei.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Application template configuration for: text-embeddings-inference
# Fill in the values below to configure your application.
# To use values from another app, use the following format:
# my_param:
#   type: "app-instance-ref"
#   instance_id: "<app-instance-id>"
#   path: "<path-from-get-values-response>"

template_name: text-embeddings-inference
template_version: v25.7.0
input:
  # Select the resource preset used per service replica.
  preset:
    # The name of the preset.
    name: ''
  # Enable access to your application over the internet using HTTPS.
  ingress_http:
    enabled: true
  # Hugging Face Model Configuration.
  model:
    # The name of the Hugging Face model.
    model_hf_name: ''
    # The Hugging Face API token.
    hf_token:
  # Configure extra arguments to pass to the server (see TEI doc, e.g. --max-client-batch-size=1024).
  server_extra_args:
  - ''
  # Additional environment variables to inject into the container. These will override any existing environment variables with the same name.
  extra_env_vars:
  -
    # Specify the name of the environment variable to inject into the container.
    name: ''
    # Specify the value of the environment variable.
    value:



```

Explanation of configuration parameters:

1. **Resource Preset**: Choose a compute preset to define the hardware resources allocated. Example: `H100x1`
2. **HTTP Authentication**: Enabled for secure access.
3. **HuggingFace Model:** Set HuggingFace model that you want to deploy.

**Step 3** — Deploy the application in your Apolo project:

```
apolo app install -f tei.yaml
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

For instructions on how to access the application, please refer to the [Usage](text-embeddings-inference.md#usage) section.

### Usage

After installation, you can utilize **Text Embeddings Inference** for different kind of workflows:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Text Embeddings Inference** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details page, find the API endpoint URL in the Outputs section. Use this URL in your API calls as shown in the script below.

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

### References:

* [https://github.com/huggingface/text-embeddings-inference](https://github.com/huggingface/text-embeddings-inference)
* [https://huggingface.co/](https://huggingface.co/)
* [Text Embedding Inference UI installation](../../../../apolo-console/apps/installable-apps/available-apps/text-embeddings-inference.md)
* [Managing Apps](../managing-apps.md)
