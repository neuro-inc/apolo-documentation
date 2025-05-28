# Stable Diffusion

Stable Diffusion is a state-of-the-art deep learning model designed for generating high-quality images from textual descriptions. It utilizes a **latent diffusion process**, a type of generative model that iteratively refines noise to produce detailed and realistic images. This model is highly efficient, scalable, and capable of running on consumer-grade hardware, making it widely used for applications in art generation, content creation, and research. Developed with open accessibility in mind, Stable Diffusion empowers developers and creators to explore generative AI while maintaining adaptability for various use cases.

Apolo utilizes [Stable Diffusion Web UI by AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui) and [StableStudio UI ](https://github.com/Stability-AI/StableStudio)to make inference/have a UI playground.

Stable Diffusion WebUI is an intuitive, browser-based interface designed for generating and managing AI-generated images using Stable Diffusion. It offers customizable workflows, advanced image settings, and integrations for various Stable Diffusion models, enabling users to create and edit images with ease.

StableStudio is a web application built to enhance the Stable Diffusion experience. It provides minimalistic UI and batch inference capability.

**Key Features**

* **API exposure:** Access Stable Diffusion models via RESTful APIs for seamless integration with other applications.
* **User-Friendly Interface:** Both platforms feature easy-to-navigate interfaces for AI image generation.
* **Customizability:** Fine-tune image outputs using adjustable parameters like prompt strength, resolution, and style preferences.
* **Multimodal Integration:** Generate images from text prompts, sketches, or existing images with advanced control over results.
* **Batch Processing:** Automate workflows for generating or editing multiple images simultaneously.

### Installation and deployment on Apolo

You can deploy Stable Diffusion WebUI using Apolo, which facilitates Helm chart deployment and integrates with other applications running on the platform. This simplifies deployment and management, allowing for easy customization and integration with your existing infrastructure.

Apolo deploys HuggingFace models.

**The Apolo installation process automates:**

* **Dockerization of inference server:** Inference Server is wrapped into Docker container which is supported by Apolo.
* **Resource Allocation:** Define resource limits (CPU, memory, GPU) using Apolo presets.
* **Persistent Storage:** Automatically provisions persistent storage for your Stable Diffusion data.
* **Ingress Configuration:** Configure ingress for external access to Weaviate's APIs.

You can deploy Stable Diffusion WebUI in 2 ways:

**Apolo Console (recommended way)**

<figure><img src="https://github.com/neuro-inc/apolo-documentation/blob/master/.gitbook/assets/image%20(258).png" alt=""><figcaption><p>Apolo console with SD</p></figcaption></figure>

**Apolo Cli**

```
apolo run --pass-config ghcr.io/neuro-inc/app-deployment -- install https://github.com/neuro-inc/app-stable-diffusion \
  stable-diffusion stable-studio charts/app-stable-diffusion \
  --timeout=900s \
  --dependency-update \
  --set "api.replicaCount=1" \  # optional, int
  --set "api.ingress.enabled=true" \ # optional, str, default=true
  --set "api.env.HUGGING_FACE_HUB_TOKEN=YOUR_TOKEN" \ # required, (Huggingface hub token https://huggingface.co/docs/hub/en/security-tokens)
  --set "preset_name=YOUR_PRESET" \ # required, str, (It is recommended to use GPU-accelerated machines)
  --set "stablestudio.enabled=true" \  # required, str (default=true)  (If you want to enable StableStudio UI playground)
  --set "stablestudio.preset_name=cpu-large" \ # if stablestudio.enabled=true, then required
  --set "model.modelHFName=stabilityai/stable-diffusion-2" \  # required, str (Huggingface model name, Example: stabilityai/stable-diffusion-2)
  --set "model.modelFiles=768-v-ema.safetensors" \  # optional, str, (Huggingface model files, model weights, comma-separated Example: 768-v-ema.safetensors)

```

### Parameters descriptions

| Parameter                        | Type    | Description                                                                                                                                         |
| -------------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `api.replicaCount`               | Integer | Optional. Number of instances. Default is 1                                                                                                         |
| `preset_name`                    | String  | Required. CPU/GPU/memory preset for the application (e.g., `a100x1`).                                                                               |
| `api.env.HUGGING_FACE_HUB_TOKEN` | String  | Required. HuggingFace token so we can pull the model                                                                                                |
| `api.ingress.enabled`            | String  | Required. If you want to expose ingress in your app.                                                                                                |
| `stablestudio.enabled`           | String  | Required. If you want to expose StableStudio UI as part of your deployment                                                                          |
| `stablestudio.preset_name`       | String  | Required. If you enabled StableStudio deployment                                                                                                    |
| `model.modelHFName`              | String  | Required. Name of the HuggingFace model (e.g. `stabilityai/stable-diffusion-2` ).                                                                   |
| `model.modelFiles`               | String  | Optional. Comma Separated list of particular model files that you will use. For example only `*.safetensors` file, so we don't pull all repository. |

### API usage

After you application is installed, you can utilize the WebUI or API endpoints exposed

**Swagger API documentation:**

https://\<APP\_HOST>>/docs

**txt2img API endpoint:** https://\<APP\_HOST>/sdapi/v1/txt2img

**Request example:**

```
curl -X 'POST' \
  'https://<APP_HOST>/sdapi/v1/txt2img' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "prompt": "Some prompt"
}'
```

### Integration with StableStudio

When your stable diffusion application is exposed, and if you deployed with StableStudio\
You can utilize StableStudio WebUI

Click settings on the right corner of your StableStudio WebUI

<figure><img src="https://github.com/neuro-inc/apolo-documentation/blob/master/.gitbook/assets/Screenshot%202025-02-04%20at%2015.45.24.png" alt=""><figcaption></figcaption></figure>

Paste external url for your stable diffusion webui HOST Url\
The status should be "Ready without history plugin"

Note: Url is persisted only on localStorage, so if you share StableStudio, it needs to be configured again\\

<figure><img src="https://github.com/neuro-inc/apolo-documentation/blob/master/.gitbook/assets/Screenshot%202025-02-04%20at%2015.53.53.png" alt=""><figcaption></figcaption></figure>

Now click Generate and enjoy StableStudio UI

### References

* [StableDiffusion repository](https://github.com/Stability-AI/StableDiffusion)
* [Stable diffusion webui Automatic repository](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
* [StableStudio repository](https://github.com/Stability-AI/StableStudio)
