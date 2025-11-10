# Stable Diffusion

Stable Diffusion is a state-of-the-art deep learning model designed for generating high-quality images from textual descriptions. It utilizes a **latent diffusion process**, a type of generative model that iteratively refines noise to produce detailed and realistic images. This model is highly efficient, scalable, and capable of running on consumer-grade hardware, making it widely used for applications in art generation, content creation, and research. Developed with open accessibility in mind, Stable Diffusion empowers developers and creators to explore generative AI while maintaining adaptability for various use cases.

To learn more about this application, visit the main [Stable Diffusion app page](../../../../apolo-console/apps/installable-apps/available-apps/stable-diffusion.md).

## Installing

Stable Diffusion can be installed on Apolo either via the CLI or the [Web Console](../../../../apolo-console/apps/installable-apps/available-apps/stable-diffusion.md#apolo-deployment). Below are the detailed instructions for installing Stable Diffusion using Apolo CLI.

### Installing via Apolo CLI

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get stable-diffusion > mystablediffusion.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Example mystablediffusion.yaml

template_name: stable-diffusion
template_version: v25.5.0
input:
  # Enable access to your application over the internet using HTTPS.
  ingress_http:
    # Require authenticated user
    auth: true
  # Select the resource preset used per service replica.
  preset:
    # The name of the preset.
    name: 'H100x1'
  # Configure the deployment settings and model selection for Stable Diffusion.
  stable_diffusion:
    # Set the number of replicas to deploy
    replica_count: 1
    # Hugging Face Model Configuration.
    hugging_face_model:
      # The name of the Hugging Face model.
      model_hf_name: "stabilityai/stable-diffusion-2"
      # The Hugging Face API token.
      hf_token:
        key: HF_TOKEN

```

Explanation of configuration parameters:

* `preset.name=gpu-l4-x1` requests one NVIDIA L4; Apolo auto‑sets `CUDA_VISIBLE_DEVICES` & default batch flags.
* `replica_count=2` launches two pods behind a load balancer for higher QPS.
* `hugging_face_model.model_hf_name` points to SD‑XL‑Turbo for faster drafts.
* `hf_token` pulls the secret string from the _HF\_TOKEN_ key in Apolo Secrets.

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f mystablediffusion.yaml
```

Monitor the application status using:

```bash
apolo app list
```

To uninstall the application, use:

```bash
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](stable-diffusion.md#usage) section.

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

After installation, you can utilize Stable Diffusion for image generation workflows and you can access it directly from your browser or using its Rest API&#x20;

To view and manage installed instances of the Stable Diffusion app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Stable Diffusion** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details page, click the **Open App** button at the top of the page to launch the application in a dedicated browser window.&#x20;

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

* **SD.Next Web UI** – the default root path (`/`).

### References

* [StableDiffusion repository](https://github.com/Stability-AI/StableDiffusion)
* [SD.Next WebUI repository](https://github.com/neuro-inc/sdnext)
* [Apolo's StableDiffusion Helm Chart repository](https://github.com/neuro-inc/app-stable-diffusion)
