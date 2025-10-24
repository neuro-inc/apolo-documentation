# Fooocus

Fooocus is a free, offline, open-source image generator designed for high-quality text-to-image creation with minimal prompt engineering or parameter tuning. It features advanced capabilities like custom inpainting and outpainting algorithms, image prompting, and prompt weighting, ensuring superior results even with simple prompts. Fooocus supports SDXL models, offers robust options for image upscaling and variation, and includes tools for negative prompts, face swapping, and textual image descriptions. Additional features like adjustable style, quality, and sampling parameters, along with multi-prompt and embedding support, make it versatile for creative applications. With a minimum GPU requirement of 4GB, it balances accessibility with powerful functionality. For more information about this application, refer to the main [Fooocus app page](../../../../apolo-console/apps/installable-apps/available-apps/fooocus.md).

### Installation

#### Install via Apolo CLI

For a detailed explanation on how you can manage installable applications using Apolo CLI, please refer to [Managing Apps](../managing-apps.md). To install Fooocus, follow these steps:

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get fooocus > myfooocus.yaml
```

**Step 2** — Customize the application parameters. Below is an example configuration file:

```yaml
# Example myfooocus.yaml

template_name: fooocus
template_version: v25.5.1
input:
  preset:
    name: 'H100x1'
  fooocus_specific:
    # Provide the Hugging Face API token for model access and integration.
    huggingface_token_secret: secret:HF_TOKEN
  # Enable access to your application over the internet using HTTPS.
  ingress_http:
    # Require authenticated user credentials with appropriate permissions for all incoming HTTPS requests to the application.
    auth: true
```

Explanation of configuration parameters:

1. **Resource Preset**: Choose a compute preset to define the hardware resources allocated. Example: `H100x1`
2. **HTTP Authentication**: Enabled for secure access.

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f myfooocus.yaml
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

For instructions on how to access the application, please refer to the [Usage](fooocus.md#usage) section.

### Usage

After installation, you can utilize Fooocus for image generation workflows and you can access it directly from your browser.&#x20;

To view and manage installed instances of the Fooocus app:

1. Go to the **Installed Apps** tab.
2. You will see a list of all running apps, including the **Fooocus** app you just installed. To open the detailed information & uninstall the app, click the **Details** button.

Once in the Details" page, scroll down to the Outputs sections. To launch the applications, find the **HTTP API** output with with the public domain address, copy and open it in a dedicated browser window.&#x20;

<figure><img src="../../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>



### References

* [Fooocus documentation](https://github.com/neuro-inc/Fooocus)
* [Fooocus installation with Apolo Flow documentation](https://github.com/neuro-inc/Fooocus/blob/apolo/APOLO.md)
