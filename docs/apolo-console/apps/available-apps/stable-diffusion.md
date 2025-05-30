# Stable Diffusion

Stable Diffusion is a state-of-the-art deep learning model designed for generating high-quality images from textual descriptions. It utilizes a **latent diffusion process**, a type of generative model that iteratively refines noise to produce detailed and realistic images. This model is highly efficient, scalable, and capable of running on consumer-grade hardware, making it widely used for applications in art generation, content creation, and research. Developed with open accessibility in mind, Stable Diffusion empowers developers and creators to explore generative AI while maintaining adaptability for various use cases.

Apolo utilizes [Stable Diffusion Web UI by AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui) and [StableStudio UI ](https://github.com/Stability-AI/StableStudio)to make inference/have a UI playground.

Stable Diffusion WebUI is an intuitive, browser-based interface designed for generating and managing AI-generated images using Stable Diffusion. It offers customizable workflows, advanced image settings, and integrations for various Stable Diffusion models, enabling users to create and edit images with ease.

StableStudio is a web application built to enhance the Stable Diffusion experience. It provides minimalistic UI and batch inference capability.

### Key Features

| Feature                            | How the Apolo App Helps                                                        |
| ---------------------------------- | ------------------------------------------------------------------------------ |
| **Text‑to‑Image & Image‑to‑Image** | Prompt → image or edit existing images via Web UI or REST.                     |
| **Multiple UIs**                   | Switch between AUTOMATIC1111 (power‑user) and StableStudio (lightweight).      |
| **Custom Models**                  | Point to any Hugging Face model (`stabilityai/stable-diffusion‑xl`, `lora/…`). |
| **GPU Presets**                    | Pick from `gpu-l4-x1`, `gpu-a100-x1`, etc.; Apolo sets env vars & drivers.     |
| **Secrets Integration**            | Store HF tokens in Apolo Secrets instead of plain text.                        |
| **Scalable Replicas**              | `replica_count` lets you handle bursty image queues.                           |



### Apolo Deployment

Deploy via either the **Web Console UI** or **Apolo CLI**.

### Web Console UI

#### 1 · Open the catalogue

<figure><img src="../../../.gitbook/assets/Screenshot 2025-05-23 at 17.35.39.png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../../.gitbook/assets/Screenshot 2025-05-23 at 17.35.09.png" alt=""><figcaption></figcaption></figure>



### Apolo CLI

Create a declarative YAML and push it with one command.

```yaml
# stable-diffusion.yaml

template_name: "stable-diffusion"
template_version: "v25.5.0"
input:
  preset:
    name: "gpu-l4-x1"         # 1× L4 24 GB
  ingress_http:
    auth: false               # public playground
  stable_diffusion:
    replica_count: 2          # autoscale to 2 replicas
    hugging_face_model:
      model_hf_name: "stabilityai/stable-diffusion-2"
      hf_token:               # reference an Apolo Secret
        key: "HF_TOKEN"

# deploy
apolo app install -f stable-diffusion.yaml \
  --cluster <CLUSTER> --org <ORG> --project <PROJECT>
```

**Explanation**

* `preset.name=gpu-l4-x1` requests one NVIDIA L4; Apolo auto‑sets `CUDA_VISIBLE_DEVICES` & default batch flags.
* `replica_count=2` launches two pods behind a load balancer for higher QPS.
* `hugging_face_model.model_hf_name` points to SD‑XL‑Turbo for faster drafts.
* `hf_token` pulls the secret string from the _HF\_TOKEN_ key in Apolo Secrets.

### Inputs / Outputs _(schema v1)_

**Inputs**

| JSON Path                                           | Default | Description             |
| --------------------------------------------------- | ------- | ----------------------- |
| `preset.name`                                       | –       | GPU preset per replica. |
| `ingress_http.auth`                                 | `true`  | Basic‑auth gate.        |
| `stable_diffusion.replica_count`                    | `1`     | Number of pods.         |
| `stable_diffusion.hugging_face_model.model_hf_name` | –       | HF model repo.          |
| `stable_diffusion.hugging_face_model.hf_token`      | `null`  | Secret or string token. |

**Outputs**

| Key                      | Purpose                                  |
| ------------------------ | ---------------------------------------- |
| `external_api.*`         | Public REST endpoint (`/v1/txt2img`).    |
| `internal_api.*`         | In‑cluster endpoint for other workloads. |
| `hf_model.model_hf_name` | Echoes the loaded model.                 |

### Usage

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

* **AUTOMATIC1111 Web UI** – the default root path (`/`).
* **StableStudio** – served at `/stablestudio` when enabled in the chart.

**Connecting StableStudio**

1. Open StableStudio and click **Settings** (gear icon, top right).
2. Paste the **external URL** of your Stable Diffusion deployment into **Host URL**.
3. Status should show _Ready without history plugin_.
4. Click **Generate** and enjoy!\
   &#xNAN;_&#x4E;ote:_ The host URL is stored only in `localStorage`; each browser or private window needs to be configured again.

### References

* [StableDiffusion repository](https://github.com/Stability-AI/StableDiffusion)
* [Stable diffusion webui Automatic repository](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
* [StableStudio repository](https://github.com/Stability-AI/StableStudio)
