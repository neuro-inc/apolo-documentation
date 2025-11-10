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

### Installation

#### Install via Apolo Web Console

For a detailed explanation on how you can manage installable applications, please refer to [Managing Apps](../managing-apps.md). To install Fooocus via Apolo Web Console, follow these steps:

**Step 1** — Navigate to the Apps page, find Jupyter from the list and click the corresponding "Install" button. This will redirect you to the installation page

**Step 2** — Configure the application by filling the required fields and click install.

**Step 3** — Click "Install" to initiate deployment. You will be redirected to the application details page where you can monitor the installation progress and view application outputs. For instructions on how to access the application, please refer to the [Usage](fooocus.md#usage) section.

#### Parameter Descriptions

The following installation options are available:

* **Resource Preset**: Choose from the dropdown menu - this is the only required field that you must set
* **Networking Settings**
  * **HTTP Authentication** - is enabled by default so you application is only accessible to users authenticated in Apolo and with permissions in the current project
* **Metadata**: Enter the application display name.

To install Fooocus via Apolo CLI, refer to [Apolo CLI Fooocus app page](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/fooocus.md).

### Usage

After installation, you can utilize Fooocus for image generation workflows and you can access it directly from your browser.&#x20;

To view and manage installed instances of the Fooocus app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Fooocus** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details page, click the **Open App** button at the top of the page to launch the application in a dedicated browser window.&#x20;

<figure><img src="../../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>



### References

* [Fooocus documentation](https://github.com/neuro-inc/Fooocus)
* [Fooocus installation with Apolo Flow documentation](https://github.com/neuro-inc/Fooocus/blob/apolo/APOLO.md)
