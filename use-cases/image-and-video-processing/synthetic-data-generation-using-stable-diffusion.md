# Synthetic Data Generation using Stable Diffusion

The example shows how to run [Stable Diffusion](https://huggingface.co/stabilityai/stable-diffusion-3-medium) model via Apolo platform and generate images for your synthetic dataset.&#x20;

### Synthetic data

Synthetic image data refers to **computer-generated visuals** that simulate real-world scenes, rather than capturing them from physical environments. Unlike traditional photographs, synthetic images do not depict a real-world moment in time but are designed based on real-world concepts. They retain the **semantic essence** of objects such as cars, tables, or houses, making them valuable for **computer vision applications**.

In essence synthetic images are pictures generated using computer graphics, simulation methods and artificial intelligence (AI), to represent reality with high fidelity.

There are plenty of applications of synthetic data in modern computer vision pipelines.

#### Why to use synthetic data in your dataset:

* **Expands Variability**: AI-generated images introduce **variations in lighting, angles, and environments** that may be missing in real-world datasets.
* **Reducing Bias**: If a dataset lacks diversity (e.g., images of cars mostly in daylight), synthetic data can **balance** the distribution by adding night-time, foggy, or rainy conditions.
* **Enhances Generalization**: Helps models perform better in **unseen scenarios** by providing edge cases not commonly found in real-world data.

### Generating images using Apolo

The example show HOWTO run [Stable Diffusion 3](https://huggingface.co/stabilityai/stable-diffusion-3-medium/tree/main) using WebUI, Cli, and API.

#### Step1: Deploy Stable Diffusion using Apolo app

[Log in](https://console.apolo.us/auth) to your Apolo account

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>Login screen</p></figcaption></figure>

Find Stable Diffusion in your Apps tab

<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Apps tab</p></figcaption></figure>

Deploy Stable Diffusion version, if you want to minimize the size, specify only weights

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption><p>Stable Diffusion installation Tab</p></figcaption></figure>

Now, check your App url at the details -> Outputs

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### Use-case:&#x20;

Stable Diffusion is often used to generate imaginative and playful images, such as a penguin skateboarding through a neon city or a cat wearing a top hat, sipping tea in a Victorian parlor. While these highlight its creative potential, this demo focuses on showcasing its practical applications for real-world business use cases, demonstrating how it can drive impactful solutions across various industries.

Imagine you're developing an **instance segmentation or object detection model** specifically for identifying and segmenting different rooms inside a house or detecting objects in the . While you already have some 2D drawings to work with, the dataset is insufficient for training an effective model. To address this limitation, generating synthetic data could be a valuable solution, helping to supplement your existing dataset and improve the model's performance by providing diverse and varied examples for training.

So by the time you have

* Lack of Real-World Data for 2d drawings.
* Data Collection is not possible.

Here synthetic data can be handy.

**We will simply define a prompt** \
Simple 2D architectural floorplan with clean, black lines on a white background. The layout should include walls, rooms, and basic features like doors and windows, all drawn with clear black lines. The floorplan should be minimalistic and easy to understand, resembling the style of technical drawings or blueprints.

**Also, it is very important to define a negative prompt**\
colors, textures, shading, and any 3D elements. furniture, patterns, or decorative designs. Exclude any details like landscapes, people, or non-architectural elements. Keep the drawing simple, with just black lines and a white background. Round shapes\


Generated example:

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>Generated Floorplan</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption><p>Some of the generated images.</p></figcaption></figure>

We can tune the prompt further, so it does not make blurry lines, and fits our needs for the detection/segmentation model.

Now Check the API&#x20;

```bash
<APP_URL>/sdapi/v1/txt2img
```

Now you can run the script and review manually the generated images.

You can adjust image size, batch\_number, steps, see [https://vladmandic.github.io/sdnext-docs/](https://vladmandic.github.io/sdnext-docs/)

Swagger documentation for the text2img parameters is here:\
\<APP\_URL>/docs

```
import requests
import base64
from PIL import Image
from io import BytesIO

# Define the URL for the API
url = "<APP_URL>/sdapi/v1/txt2img"

# Define the headers
headers = {
    'Accept': 'application/json',
    'Content-Type': 'application/json'
}

# Define the base data template
base_req_body = {
    "prompt": "Simple 2D architectural floorplan with clean, black lines on a white background. The layout should include walls, rooms, and basic features like doors and windows, all drawn with clear black lines. The floorplan should be minimalistic and easy to understand, resembling the style of technical drawings or blueprints. ",
    "negative_prompt": "colors, textures, shading, and any 3D elements. furniture, patterns, or decorative designs. Exclude any details like landscapes, people, or non-architectural elements. Keep the drawing simple, with just black lines and a white background. Round shapes",
    "batch_size": 1,
    "steps": 20
}


# Function to send request and parse base64 image response
def generate_and_parse_image(loop_number):
    # Send the POST request to the API
    response = requests.post(url, json=base_req_body, headers=headers)

    # Check if the request was successful
    if response.status_code == 200:
        # Assuming the response contains a base64-encoded image string
        response_json = response.json()
        images = response_json.get('images')  # 'image' is the key for base64 string

        if images:
            for img_batch_num, image in enumerate(images):
                # Decode the base64 string to bytes
                image_data = base64.b64decode(image)

                # Convert the byte data to an image
                image = Image.open(BytesIO(image_data))

                # Construct the file name
                filename = f"loop_{loop_number}_img_batch_{img_batch_num}.png"

                # Save the image to a file
                image.save(filename)
                print(f"Image saved as '{filename}'")
    else:
        print(
            f"Request failed for loop {loop_number}, with status code {response.status_code}: {response.text}")


if __name__ == '__main__':

    # Loop for 10 iterations
    for i in range(1, 11):
        # Generate the image and save it
        generate_and_parse_image(i)

```

### References

* [SDNext repository](https://github.com/neuro-inc/sdnext)
* [Stability AI](https://stability.ai/) website
* [Stability AI HuggingFace](https://huggingface.co/stabilityai)
* [Stable Diffusion prompt guide](https://stability.ai/learning-hub/stable-diffusion-3-5-prompt-guide)

