# Stable Diffusion

Stable Diffusion is a state-of-the-art deep learning model designed for generating high-quality images from textual descriptions. It utilizes a **latent diffusion process**, a type of generative model that iteratively refines noise to produce detailed and realistic images. This model is highly efficient, scalable, and capable of running on consumer-grade hardware, making it widely used for applications in art generation, content creation, and research. Developed with open accessibility in mind, Stable Diffusion empowers developers and creators to explore generative AI while maintaining adaptability for various use cases.

Apolo uses [SD.Next](https://github.com/neuro-inc/sdnext)[ ](https://github.com/Stability-AI/StableStudio)as the WebUI for the Stable Diffusion application.

SD.Next is a browser-based interface designed for generating and managing AI-generated images using Stable Diffusion and other models. It offers several customization options, advanced image settings, and integrations for various Stable Diffusion models, enabling users to create and edit images with ease. It also provides an [API](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/API) for generating images which can be integrated into other applications.

## Key Features

| Feature                            | How the Apolo App Helps                                                        |
| ---------------------------------- | ------------------------------------------------------------------------------ |
| **Text‑to‑Image & Image‑to‑Image** | Prompt → image or edit existing images via Web UI or REST.                     |
| **Custom Models**                  | Point to any Hugging Face model (`stabilityai/stable-diffusion‑xl`, `lora/…`). |
| **GPU Presets**                    | Pick from `gpu-l4-x1`, `gpu-a100-x1`, etc.; Apolo sets env vars & drivers.     |
| **Secrets Integration**            | Store HF tokens in Apolo Secrets instead of plain text.                        |
| **Scalable Replicas**              | `replica_count` lets you handle bursty image queues.                           |



## Installing

Stable Diffusion can be installed on Apolo either via the [CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/stable-diffusion.md) or the Apolo Console. Below are the detailed instructions for installing Stable Diffusion using Apolo Console.

### Installing via Apolo Console

#### 1 · Open the catalogue

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-23 at 17.35.39.png" alt=""><figcaption></figcaption></figure>

\
2 · Configure the wizard

| Section                 | Field                                           | Example                                             | Notes |
| ----------------------- | ----------------------------------------------- | --------------------------------------------------- | ----- |
| **Enable HTTP Ingress** | `auth = true`                                   | Adds basic‑auth to the public URL.                  |       |
| **Resource Preset**     | `gpu-a100-x1`                                   | 1× NVIDIA A100 80 GB (or choose `gpu-l4-x1`, etc.). |       |
| **Stable Diffusion**    | `replica_count = 1`                             | Increase for concurrent jobs.                       |       |
| **Hugging Face Model**  | `stabilityai/stable-diffusion-2-1-unclip-small` | Any HF repo path.                                   |       |
| **HF Token**            | Secret `HF_TOKEN`                               | Needed for private or gated models.                 |       |

Click **Install**. Wait until _Status → healthy_; copy the **external\_api** host (REST) or open the Web UI at `https://sd-<id>.apps.<cluster>.apolo.us`.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-23 at 17.35.09.png" alt=""><figcaption></figcaption></figure>

### Usage

After installation, you can utilize Stable Diffusion for image generation workflows and you can access it directly from your browser or using its Rest API&#x20;

To view and manage installed instances of the Stable Diffusion app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Stable Diffusion** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details" page, scroll down to the Outputs sections. To launch the applications, find the **HTTP API** output with with the public domain address, copy and open it in a dedicated browser window.&#x20;

#### 1 · REST API

Once the app is **healthy** Apolo exposes auto‑generated **Swagger** docs at:

```
https://<APP_HOST>/docs
```

**txt2img endpoint**

<pre><code><strong>curl -X POST \
</strong>  -H "Authorization: Basic $(apolo config show-token)" \
  -H "accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
        "prompt": "Some prompt"
      }' \
</code></pre>

The response contains a base64‑encoded PNG under `images[0]`.

#### 2 · Web UIs

* **SD.Next WebUI** – the default root path (`/`).



### References

* [StableDiffusion repository](https://github.com/Stability-AI/StableDiffusion)
* [SD.Next WebUI repository](https://github.com/neuro-inc/sdnext)
* [Apolo's StableDiffusion Helm Chart repository](https://github.com/neuro-inc/app-stable-diffusion)
