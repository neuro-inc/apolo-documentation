# HOWTO: Lora models with Stable Diffusion

### What is LoRA (Low-Rank Adaptation)?

**LoRA**, short for **Low-Rank Adaptation**, is a technique used to **fine-tune large AI models** (like language or vision models) **efficiently and with fewer resources**.

Instead of updating all the parameters in a massive pre-trained model—which is expensive and memory-intensive—LoRA freezes the original model and **adds small, trainable layers** (called _low-rank matrices_) to specific parts of the model (like attention layers). These additions learn the task-specific changes, allowing the core model to remain unchanged.

Using **LoRA models with Stable Diffusion** is a super popular way to customize the style, character, or theme of your image generations without retraining the whole model.

### How to Use LoRA in Apolo platform with Stable Diffusion

Prerequisites:

* Install Apolo cli using [this](https://docs.apolo.us/index/apolo-concepts-cli/installing) doc
* Clone [this](https://github.com/neuro-inc/sdnext) repo

#### Add secret value with personal Hugging Face Token&#x20;

```
apolo secret add HF_TOKEN <your_token>
```

**Run Stable Diffusion job, replacing the secret value, preset, and any other configuration**

```
apolo-flow run stablediffusion
```

**Download the model using SDnext interface**

Go to Models -> HuggingFace

<figure><img src="../.gitbook/assets/image (30).png" alt=""><figcaption><p>HuggingFace model download interface</p></figcaption></figure>

Generate the first image:

**Prompt:**

Ghibli style futuristic stormtrooper with glossy white armor and a sleek helmet, standing heroically on a lush alien planet, vibrant flowers blooming around, soft sunlight illuminating the scene, a gentle breeze rustling the leaves

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

### Lora model

Let's find a trained model on Civit.ai, we need to filter model by our Stable Diffusion version.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption><p>Civit.AI search models interface</p></figcaption></figure>

Alternative can be HuggingFace search. (tags: Lora, Stable Diffusion, 3.5)

I will use "[studio-ghibli-style-lora](https://huggingface.co/alvarobartt/ghibli-characters-sd3.5-lora)" Lora model.

Now we need to copy our model into the /Lora directory of our storage volume

```
apolo cp -r studio-ghibli-style-lora.safetensors storage:sdnext/models/Lora/studio-ghibli-style-lora.safetensors
```

After Model Copying, and hitting the refresh button on the Lora tab, we should be able to see our model downloaded.

Click on your Networks -> Lora tab, hit refresh, and click on your Lora model, that will add Lora to youre prompt, and you will be able to generate images using it.

For example we generated ghibli-style stormtrooper.

<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption><p>Ghibli style stormtrooper</p></figcaption></figure>

### References:

* [Apolo cli](https://docs.apolo.us/index/apolo-concepts-cli/installing)
* [SDnext repository](https://github.com/neuro-inc/sdnext)
* [Apolo secrets cli documentation](https://docs.apolo.us/index/apolo-cli/commands/secret)
* [Civit AI model storage](https://civitai.com/)



