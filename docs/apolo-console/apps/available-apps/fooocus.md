# Fooocus

Fooocus is a free, offline, open-source image generator designed for high-quality text-to-image creation with minimal prompt engineering or parameter tuning. It features advanced capabilities like custom inpainting and outpainting algorithms, image prompting, and prompt weighting, ensuring superior results even with simple prompts. Fooocus supports SDXL models, offers robust options for image upscaling and variation, and includes tools for negative prompts, face swapping, and textual image descriptions. Additional features like adjustable style, quality, and sampling parameters, along with multi-prompt and embedding support, make it versatile for creative applications. With a minimum GPU requirement of 4GB, it balances accessibility with powerful functionality.

### Key Features

* **High-Quality Text-to-Image Generation**
  * Requires minimal prompt engineering or parameter tuning.
  * Supports prompts ranging from simple phrases to detailed descriptions (up to 1000 words).
* **Image Upscaling and Variations**
  * Options for upscaling (1.5x, 2x) or creating subtle/strong variations.
* **Inpainting and Outpainting**
  * Custom algorithms and models for enhanced results compared to standard SDXL methods.
* **Image Prompt Support**
  * Unique algorithm for better quality and prompt understanding compared to standard methods.
* **Advanced Features**
  * Style options, guidance settings, quality adjustments, and aspect ratio controls.
* **Multi-Prompt Support**
  * Enables multiple lines of prompts and prompt weighting (e.g., `I am (happy:1.5)`).
  * Includes reweighting algorithms for compatibility with popular prompt formats like A1111.
* **Negative Prompts**
  * Allows specifying what should be avoided in the generated images.
* **Face Swapping**
  * Uses InsightFace for advanced face swap functionality.
* **Image Descriptions**
  * Generate textual descriptions from input images.
* **Support for SDXL Models**
  * Compatible with SDXL models from platforms like Civitai.
* **Custom Sampling Parameters**
  * Fine-tune output with adjustable contrast, sharpness, and other parameters.
* **ControlNet Integration**
  * User-friendly interface for advanced input image controls.
* **Prompt Embedding Support**
  * Use embeddings directly within prompts for precise control.
* **Batch Image Generation**
  * Easily generate multiple images by specifying the desired quantity.

### Installation and Deployment

You can deploy Fooocus using Apolo Flow, which facilitates application chart deployment and integrates with other applications running on Apolo.

The installation process automates:

1. Application deployment in cluster
2. Fooocus configs to persist outputs in Apolo Files
3. Web interface ingress configuration

#### Parameter Descriptions

| Parameter     | Type   | Description                                                          |
| ------------- | ------ | -------------------------------------------------------------------- |
| `preset_name` | String | Required. CPU/GPU/memory preset for theapplication (e.g., `a100x1`). |



### References

* [Fooocus documentation](https://github.com/neuro-inc/Fooocus)
* [Fooocus installation with Apolo Flow documentation](https://github.com/neuro-inc/Fooocus/blob/apolo/APOLO.md)
