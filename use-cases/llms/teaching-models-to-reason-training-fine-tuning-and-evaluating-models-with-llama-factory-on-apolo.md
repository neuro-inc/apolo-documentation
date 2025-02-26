---
description: This is a tutorial for training, fine-tuning and evaluating models.
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
✅ **An Apolo project is set up** (will use current project if no project is set)\
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

```yaml
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
</strong>
"open-r1/OpenR1-Math-220k": {
      "hf_hub_url": "open-r1/OpenR1-Math-220k", 
      "formatting": "sharegpt",
      "ranking": false,
      "split": "train", // use test split if available in the dataset
      "columns": {
        "messages": "messages"  
      },
      "tags": {
        "role_tag": "role",
        "content_tag": "content",
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

* Copy necessary datasets and configurations to Apolo storage.
* Start the **llama\_factory\_webui** job.

**Output Example**:

```bash
√ Job ID: job-f6522c71-1912-4775-962e-19f3c1357224
√ Name: llama-factory
√ Status: running
√ Http URL: https://llama-factory--0214c319f8.jobs.<your_cluster_name>.org.apolo.us
```

#### **Step 4: Access the Web UI**

**You can either use the URL above, which is part of the deployment output or wait for a new tab to open automatically with the Llama Factory Web UI, as seen below.**



<figure><img src="../.gitbook/assets/Screenshot 2025-02-13 at 14.14.35.png" alt=""><figcaption></figcaption></figure>

🎉 **You are now ready to train and fine-tune models!**

### **2. Training & Fine-Tuning Models with LLaMA Factory**

#### **Option 1: Fine-Tuning Using the Web UI**

Once inside the Web UI:

**Step 1: Select Base Model**

* Choose a **base model** (e.g., `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B, Llama-3.2-3B`).
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

### 3. Chat

After the model finishes training, you can move to the Chat tab and follow the steps below:

* select the model&#x20;
* select the checkpoint you want to test
* write your query in the Input section and submit the query

Here you can try the model out. As you can see below, it has already started showing reasoning:

<figure><img src="../.gitbook/assets/Screenshot 2025-02-24 at 18.01.38 (1).png" alt=""><figcaption></figcaption></figure>

After it finishes, you can expand the **Though** section to see all of the reasoning tokens.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-25 at 18.58.06.png" alt=""><figcaption></figcaption></figure>

### 4. Evaluating Your Fine‐Tuned/Trained Model

Once your training (or fine‐tuning) has completed, you can **evaluate** the new model from within the Web UI:

1. **Navigate to the “Evaluate & Predict” Tab**\
   From the top menu in LLaMA Factory (Train / Evaluate & Predict / Chat / Export), click **Evaluate & Predict**.
2. **Select Your Checkpoint**
   * **Model name**: Choose the same base model used for fine‐tuning (e.g., `Llama-3.2-3B` ).
   * **Checkpoint path**: Pick the checkpoint directory you just trained (e.g., `train_2025-02-17-12-36-51`).
3. **Configure the Dataset for Evaluation**
   * **Data dir**: Set to the directory containing your dataset files (e.g., `data`).
   * **Dataset**: Select the dataset entry you want to evaluate on (e.g., `open-r1/OpenR1-Math-220k`).
   * Use the **Preview dataset** button (optional) to confirm that the dataset is recognized properly (e.g., no `'from'` KeyError, or other format issues).
4. **Adjust Evaluation Settings**
   * **Cutoff length**: The maximum token length in your input sequences (e.g., 1024).
   * **Max samples**: How many data samples to evaluate (e.g., 1000).
   * **Batch size**: The number of samples processed simultaneously per GPU (e.g., 2 or 4, depending on GPU memory).
   * **Save predictions**: Tick this box to store the model outputs on each sample.
   * **Maximum new tokens**: How many tokens the model may generate for each example (e.g., 512).
   * **Top‐p** and **Temperature**: Sampling hyperparameters—adjust for different sampling behaviors (e.g., `top_p=0.7`, `temperature=0.95`).
   * **Output dir**: Where the evaluation logs and predictions will be saved.
5. **Start the Evaluation**
   * Click **Start**.
   * Monitor logs in the console below the Start button.
   * Once finished, LLaMA Factory will show the evaluation progress and any metrics (if the dataset has built‐in metrics).
   * If **Save predictions** was checked, look for a file (e.g., `.jsonl` or `.csv`) in the `output` directory with each sample’s output.
6. **Review Results**
   * If your dataset or LLaMA Factory supplies metrics (like accuracy, perplexity, or F1), they will appear in the logs or console.
   * You can also open the saved predictions file to see the raw completions on each sample.

> **Tip**: If your dataset does not provide built‐in metrics, you will see only raw predictions. You can parse these to do custom scoring if needed.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-26 at 15.40.05.png" alt=""><figcaption></figcaption></figure>

Final results will be visible after the evaluation finishes, it's a good practice to evaluate model you're starting off with and then evaluate the fine-tuned/trained one so you can understand if results improved or not.

<figure><img src="../.gitbook/assets/Screenshot 2025-02-26 at 16.14.18.png" alt=""><figcaption></figcaption></figure>

### 5. Exporting the model&#x20;

For exporting models, you need to switch to the export tab and follow these steps:&#x20;

* Set the export dir (where you want the model to be saved in the mapped volume); you can later download it from this directory; it's recommended to be under one of the mounted volumes in the `.apolo/live.yaml`&#x20;

```yaml
output:
  remote: storage:$[[ flow.project_id ]]/output
  mount: /app/saves
  local: output
```

* you can also provide the HF Hub ID, if you want the model to be uploaded to that Huggingface Repo

<figure><img src="../.gitbook/assets/Screenshot 2025-02-25 at 19.09.52.png" alt=""><figcaption></figcaption></figure>

After exporting the model on Huggingface, you can deploy it also using the vLLM app



















