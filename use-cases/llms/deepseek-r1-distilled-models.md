---
icon: whale
---

# DeepSeek-R1 distilled models

Large language models (LLMs) have become essential tools for AI applications, but deploying them efficiently remains a challenge. [DeepSeek](https://huggingface.co/collections/deepseek-ai/deepseek-r1-678e1e131c0169c0bc89728d) Distilled models offer a balance between performance and efficiency, making them ideal for various real-world use cases.

In this guide, we will walk through deployment process of DeepSeek Distilled LLMs using [vLLM](https://github.com/vllm-project/vllm) on the **Apolo** platform.

Apolo simplifies deployment by handling:

* **Resource Allocation** – Ensuring requested GPU and CPU are always available.
* **Workload Scheduling** – Dynamically managing inference jobs.
* **Orchestration & Isolation** – Serving models endpoints securely in isolated environments.
* **Ingress & Authentication** – Providing built-in API access control and request routing.

## Hardware Considerations

Since Apolo abstracts infrastructure management, you won’t need to configure hardware manually. Instead, you operate on resource presets that incorporate a collection of resources available at runtime. For high-performance inference, consider utilizing Apolo resource preset with:

* **GPU:** NVIDIA A100 (40GB), H100, or RTX 4090. VRAM requirements depend on the model and the required context length. Minimum of 24GB VRAM recommended.
* **CPU:** At least 8 vCPUs for handling requests efficiently.
* **RAM:** 32GB+ for handling larger batch sizes or concurrent requests.
* **Storage:** consider utilizing Apolo storage that is backed by high-throughput SSD/NVMe to persist model binaries and for faster startups.

In this guide we will be outlining 3 distinct ways to deploy Large Language Models on Apolo platform, you can choose one that suits you most or try all three:

* **Deploy with CLI**
* **Deploy with Apolo-Flow**
* **Deploy with Apps GUI**

By the end of this guide, you’ll have a fully operational DeepSeek Distilled model deployed on Apolo, serving inference requests efficiently with vLLM.

Let’s get started!

## Deploy with Apolo CLI

The easiest way to start the server is to utilize CLI.

First, make sure you've completed the getting-started guide from the main documentation page since here we assume that you already installed Apolo CLI, logged into the platform, created (or joined ) organization and project.

Second, pick the resource preset you want to run you server on. For this, checkout the Apolo cluster settings either in web console, or via CLI command `apolo config show`.

In this example, we have the following presets that could do the work:

```bash
$ apolo config show
User Configuration:                                         
...
Resource Presets:                                                                   
 Name          #CPU     Memory   GPU                                Credits per hour 
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
...
 H100x1        25.0   250.0 GB   Nvidia: 1 x Nvidia-DGX-H100-80GB   6.08             
 H100x2        50.0   500.0 GB   Nvidia: 2 x Nvidia-DGX-H100-80GB   12.16            
 H100x4       100.0     1.0 TB   Nvidia: 4 x Nvidia-DGX-H100-80GB   24.32            
 dgx            220     2.1 TB   Nvidia: 8 x Nvidia-DGX-H100-80GB   48.64  
```

### deepseek-ai/DeepSeek-R1-Distill-Llama-70B

70B Llama model requires around 140 GB of vRAM in FP16 format. Therefore, we use H100x2 here.

To start vLLM server, use the following CLI command:

{% code fullWidth="true" %}
```bash
$ apolo run -s H100x2 -v storage:hf-cache:/root/.cache/huggingface --http-port 8000 vllm/vllm-openai -- '
    --model=deepseek-ai/DeepSeek-R1-Distill-Llama-70B 
    --dtype=half 
    --tensor-parallel-size=2 
    --max-model-len=1000'
```
{% endcode %}

The rest will be done by the platform: it allocates the resources, creates needed job, starts the server, exposes the endpoint, collects the metrics and so on...

Brief break down of the command:

* `-s H100x2` specifies the preset hardware config name that will be used while running the job.
* `-v storage:hf-cache:/root/.cache/huggingface` mounts the platform storage folder `hf-cache` into the job's filesystem under /root/.cache/huggingface. This is where vLLM looks for model binaries on the startup.
* `--http-port 8000` tells Apolo which port from within the job to expose.
* `vllm/vllm-openai`is a container image name, used to run the job.

The rest of the command are vLLM arguments. Full description could be found in the corresponding [section](https://docs.vllm.ai/en/latest/serving/openai_compatible_server.html#cli-reference) of vLLM documentation.

Upon the successful activation of the command you should see the following output:

```bash
√ Job ID: job-5ce4cc22-11e8-457a-8c70-bc1af64a2b3a
- Status: pending Creating
- Status: pending Scheduling
- Status: pending Pulling (Pulling image "vllm/vllm-openai:latest")
√ Status: running
√ Http URL: https://job-5ce4cc22-11e8-457a-8c70-bc1af64a2b3a.jobs.cl1.org.apolo.us
√ =========== Job is running in terminal mode ===========
...
```

Note the 'Http URL'. - this is the endpoint to direct your queries to once the job is fully up and running.

You will see the following line when the server is ready to accept requests:

```
...(other server logs)...
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### Query the model

When the model is up and running, you could use any standard client to query it. In this example, we use `curl`to send the request:

{% code fullWidth="true" %}
```bash
curl -s https://job-5bd3ab12-7c94-4e3a-85eb-e56e6320c1b0.jobs.cl1.org.apolo.us/v1/completions \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer `apolo config show-token`" \
    -d '{
        "model": "deepseek-ai/DeepSeek-R1-Distill-Llama-70B",
        "prompt": "San Francisco is a",
        "max_tokens": 100,
        "temperature": 0
    }' | jq
{
  "id": "cmpl-b866939698bd4cc59e5ffbb4548ecba0",
  "object": "text_completion",
  "created": 1739486182,
  "model": "deepseek-ai/DeepSeek-R1-Distill-Llama-70B",
  "choices": [
    {
      "index": 0,
      "text": " city that is known for its iconic landmarks, cultural diversity, and vibrant neighborhoods.",
      "logprobs": null,
      "finish_reason": "length",
      "stop_reason": null,
      "prompt_logprobs": null
    }
  ],
  "usage": {
    "prompt_tokens": 5,
    "total_tokens": 105,
    "completion_tokens": 100,
    "prompt_tokens_details": null
  }
}
```
{% endcode %}

Note, the usage of authorization header ("Authorization: Bearer...") is obligatory here, however, you could disable it. See the description of all possible job parameters and configurations in Apolo CLI documentation.

To cleanup hit ctrl+c in the console window, where the job was launched. Alternatively, use cli command:

`apolo kill <job-id>`

to terminate the job.

This is it! You now have a fully operational DeepSeek Distilled model deployed on Apolo, serving inference requests efficiently with vLLM!

## Deploy with Apolo Flow

Apolo Flow allows one to template the workloads configuration into config files. By the end of the getting-started guide, you should be able to create and run a flow from the template. The below scenario combines exposing DeepSeek Distilled model and making querying it in browser available via custom WebUI interface.

For this scenario, we need to create a flow with the following components:

* Apolo storage volumes that host model binaries and web server files
* vLLM server that serves the requests, exactly the same as we did previously
* [OpenWebUI](https://openwebui.com/) server that acts as a web interface where you could chat with your model.

Here is the configuration file snipped that implements those things:

```yaml
kind: live

volumes:
  webdata:
    remote: storage:$[[ flow.project_id ]]/web
    mount: /app/backend/data
    local: webdata
  hf_cache:
    remote: storage:$[[ flow.project_id ]]/cache
    mount: /root/.cache/huggingface

jobs:
  web:
    image: ghcr.io/open-webui/open-webui:v0.5.11
    volumes:
      - ${{ volumes.webdata.ref_rw }}
    http_port: 8080
    preset: cpu-small
    detach: true
    browse: true
    env:
      WEBUI_SECRET_KEY: "apolo"
      OPENAI_API_BASE_URL: http://${{ inspect_job('vllm').internal_hostname_named }}:8000/v1 # inspects vllm job in runtime to derive endpoint domain address

  vllm:
    image: vllm/vllm-openai:v0.7.2
    preset: ${{ params.preset }}
    http_port: "8000"
    detach: true
    volumes:
      - ${{ volumes.hf_cache.ref_rw }}
    env:
      HF_TOKEN: secret:HF_TOKEN # your huggingface token used to pull models, could be added with `apolo secret add HF_TOKEN <token>`
    params:
      preset: H100x2 # replace the preset during the job startup via `apolo-flow run vllm --param preset <preset-name>`
    cmd: >
      --model deepseek-ai/DeepSeek-R1-Distill-Llama-70B 
      --tokenizer deepseek-ai/DeepSeek-R1-Distill-Llama-70B
      --dtype=half
      --tensor-parallel-size=2
      --max-model-len=10000
```

You could find full configuration file in our GitHub [repository.](https://github.com/neuro-inc/mlops-open-webui)

The overall description of flow configuration syntax could be found in a dedicated documentation page. Let's now start `vllm`and `web` jobs.

```bash
$ apolo-flow run vllm
√ Job ID: job-73404eb1-1606-4c46-9641-e4895cc2010a
√ Name: openwebui-vllm
- Status: pending Creating
- Status: pending Scheduling
√ Status: running
√ Http URL: https://vllm--0e1436ea6a.jobs.cl1.org.apolo.us
√ The job will die in 10 days. See --life-span option documentation for details.
```

You can observe server startup process by connecting to the logs stream using cli command:

`apolo-flow logs vllm`.

### Query the model

In this case, we describe another approach to "consume" the service provided by the LLM inference server. Here we start the OpenWebUI web server, that resembles OpenAI's ChatGPT application. It allows you to chat with various LLM servers at the same time, including OpenAI-compatible (which vLLM is) and Ollama APIs.

To start web chat with your DeepSeek model, issue cli command:

`apolo-flow run web --param server vllm`

```bash
$ apolo-flow run web
√ Job ID: job-df1f8339-6c99-4ee8-b425-1daa7e023d8b
√ Name: openwebui-web
- Status: pending Creating
- Status: pending Scheduling
- Status: pending Pulling (Pulling image "ghcr.io/open-webui/open-webui:v0.5.11")
- Status: pending ContainerCreating
√ Status: running
√ Http URL: https://openwebui-web--0e1436ea6a.jobs.cl1.org.apolo.us
√ The job will die in 10 days. See --life-span option documentation for details.
```

Now the mentioned above OpenWebUI job's URL will be automatically opened in your default browser. After you log into the system, you will be able to chat with your model. Just make sure vllm finished it startup.

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption><p>OpenWebUI attached to vLLM serving deepseek-ai/DeepSeek-R1-Distill-Llama-70B</p></figcaption></figure>

This is it! You have crated and ran a flow from template, your flow has spun up a DeepSeek distilled model using vLLM and exposed its interface via web browser using OpenWebUI web-server app.

## Deploy with Apolo Apps GUI

Apolo applications allow you to deploy various systems together with their auxiliary resources as a holistic application. This also includes LLM model inference servers that enables scaling and reliability required for the production-ready projects.

The deployment process itself is quite straightforward: navigate to the Apolo web console, select LLM Inference application and start installation.

The following configuration screenshot resembles the previously discussed case:

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption><p>LLM Inference application configuration</p></figcaption></figure>

After the installation completes, you can find the endpoint for inference in application outputs.

## Deploy other DeepSeek-R1 distilled models

To deploy any other model, simply derive the amount of required vRAM to fit the model. Afterwards, select a corresponding resource preset and adjust vLLM CLI arguments. For vLLM you might need to tweak the number tensor parallel replicas as well as context length (max-model-len).

The rest stays the same — Apolo takes care of operations leaving you focused on the job.
