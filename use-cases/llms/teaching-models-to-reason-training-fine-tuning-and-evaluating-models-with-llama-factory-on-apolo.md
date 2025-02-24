---
description: This is a tutorial for training, fine-tuning and evaluating models.
hidden: true
---

# Teaching Models To Reason - Training, Fine-Tuning, and Evaluating Models with LLaMA Factory on Apolo

This tutorial will guide you through **training, fine-tuning, and evaluating models** using **LLaMA Factory on the Apolo platform**. By the end, you will be able to:

* **Deploy LLaMA Factory on Apolo**
* **Train and fine-tune models using Web UI or CLI**
* **Evaluate and test your trained models**
* **Serve the fine-tuned model via API for real-world applications**

### **Prerequisites**

Before proceeding, ensure you have: \
✅ **Apolo CLI installed** (Follow the Apolo CLI[ Installation Guide](https://docs.apolo.us/index/cli/installing))\
✅ [**Docker**](https://docs.docker.com/engine/install/) **installed** for building images \
✅ **An Apolo project set up** (will use default project if no project is set)\
✅ **A HuggingFace token set as a secret** You can do that by running the following command

```
apolo secret add HF_TOKEN <your_token>
```

### **1. Deploying LLaMA Factory on Apolo**

#### **Step 1: Clone the LLaMA Factory Repository**

```bash
git clone https://github.com/neuro-inc/LLaMA-Factory
cd LLaMA-Factory
```

Inside the apolo branch you will notice a  .`apolo`  folder containing a `live.yaml`. You can find more on the structure of the file below [here](https://docs.apolo.us/index/apolo-flow-reference/workflow-syntax/live-workflow-syntax).

```
kind: live
title: llama_factory

defaults:
  life_span: 5d

images:
  llama_factory:
    ref: image:$[[ project.id ]]:v1
    dockerfile: $[[ flow.workspace ]]/docker/docker-cuda/Dockerfile
    context: $[[ flow.workspace ]]/

volumes:
  hf_cache:
    remote: storage:$[[ flow.project_id ]]/hf_cache
    mount: /root/.cache/huggingface
    local: hf_cache
  data:
    remote: storage:$[[ flow.project_id ]]/data
    mount: /app/data
    local: data
  output:
    remote: storage:$[[ flow.project_id ]]/output
    mount: /app/saves
    local: output

jobs:
  llama_factory_webui:
    image: ${{ images.llama_factory.ref }}
    name: llama-factory
    preset: H100X1
    http_port: "7860"
    detach: true
    browse: true
    env:
      HF_TOKEN: secret:HF_TOKEN
    volumes:
      - ${{ upload(volumes.data).ref_rw }}
      - ${{ volumes.output.ref_rw }}
      - ${{ volumes.hf_cache.ref_rw }}
    cmd: bash -c "cd /app && llamafactory-cli webui"

```

Key fields to consider when deploying LlamaFactory on Apolo:

* `preset`  In this case we have H100X1 (Requests a single NVIDIA H100 GPU.)\
  to list all available presets on your cluster you can use:

```
apolo config show
```

* `data` volume, this will upload everything you have in the data folder to the jobs `/app/data`
* `output` volume, this is where you can save your checkpoints and training artifacts, the job will write to this folder /app/saves

#### **Step 2: Build the LLaMA Factory Docker Image on Apolo**

```bash
apolo-flow build llama_factory
```

This command:

* Uses the **Dockerfile configured for CUDA** (located at `docker/docker-cuda/Dockerfile`).
* Pushes the built image to the **Apolo image registry**.

**Output Example**:

```bash
[0600] Pushing image to registry.<your_cluster_name>.org.apolo.us/apolo/<your_project_name>/llama_factory:v1 
INFO[0611] Pushed registry.<your_cluster_name>.org.apolo.us/apolo/<your_project_name>/llama_factory@sha256:6cc93e18...
INFO: Successfully built image: llama_factory:v1
```

**Note:**  If you need to work with other datasets that are not supported by default, you should add support for them by extending the `dataset_info.json`. This file allows for field mapping and format type selection. I added open-r1/OpenR1-Math-220k, a reasoning dataset that will be used to finetune a Llama 3B model to enhance its reasoning capabilities.

<pre class="language-json"><code class="lang-json"><strong>{
</strong>"open-r1/OpenR1-Math-220k": {
    "hf_hub_url": "open-r1/OpenR1-Math-220k",
    "formatting": "sharegpt", 
    "ranking": false,
    "columns": {
        "messages": "messages"
    },
    "tags": {
        "role_tag": "from",
        "content_tag": "value",
        "user_tag": "user",
        "assistant_tag": "assistant",
        "system_tag": "system" 
    }
  }
}

</code></pre>



#### **Step 3: Run the LLaMA Factory Web UI**

```bash
apolo-flow run llama_factory_webui
```

This will:

* Start the **llama\_factory\_webui** job.
* Copy necessary datasets and configurations to Apolo storage.

**Output Example**:

```bash
√ Job ID: job-f6522c71-1912-4775-962e-19f3c1357224
√ Name: llama-factory
√ Status: running
√ Http URL: https://llama-factory--0214c319f8.jobs.<your_cluster_name>.org.neu.ro
```

#### **Step 4: Access the Web UI**

**You can either use the URL above that is part of the deployment output or:**

1. Open **Apolo web interface** in your browser.
2. Navigate to your **Apolo project**.
3. Locate the **llama\_factory\_webui** job. (You can also view logs of the job on the logs tab)
4.  Click **“Browse”** to open the Web UI at:

    ```
    https://llama-factory--<unique-id>.jobs.<your_cluster_name>.org.neu.ro
    ```
5. 🎉 **You are now ready to train and fine-tune models!**

In both cases, you should see a view like the one below.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-13 at 14.14.35.png" alt=""><figcaption></figcaption></figure>

### **2. Training & Fine-Tuning Models with LLaMA Factory**

#### **Option 1: Fine-Tuning Using the Web UI**

Once inside the Web UI:

**Step 1: Select Base Model**

* Choose a **base model** (e.g., `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`).
* Specify the **model path** (Hugging Face model).

**Step 2: Choose Training Method**

* **Full** (for training all parameters).
* **LoRA** (lightweight, efficient fine-tuning).
* **Freeze (freeze certain layers, there's a section on freezing layers in the UI).**

**Step 3: Configure Dataset**

* Select a **built-in dataset** or provide a **custom dataset,** we''ll go with the open-r1/OpenR1-Math-220k dataset for which we added support.
* Click **"Preview Dataset"** to verify.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-18 at 13.29.56.png" alt=""><figcaption></figcaption></figure>

**Step 4: Set Training Hyperparameters**

| Parameter                 | Recommended Value         |
| ------------------------- | ------------------------- |
| **Learning Rate**         | `5e-5`                    |
| **Epochs**                | `3`                       |
| **Batch Size**            | `2` (adjust based on GPU) |
| **Gradient Accumulation** | `4` (for small GPUs)      |
| **Scheduler**             | `cosine`                  |
| **Compute Type**          | `bf16` (for modern GPUs)  |

**Step 5: Start Training**

* Click **Start** to launch fine-tuning.
* Monitor logs and **loss curves** to track progress.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-18 at 13.31.08.png" alt=""><figcaption></figcaption></figure>







