---
icon: whale
---

# DeepSeek-R1 model deployment

In this guide, we will walk through deployment process of DeepSeek R1 model using [vLLM](https://github.com/vllm-project/vllm) and [Ray](https://docs.ray.io/en/latest/ray-overview/getting-started.html) on the **Apolo** platform.

**DeepSeek-R1** is a large language model that, [according to it's developers](https://github.com/deepseek-ai/DeepSeek-R1/blob/main/DeepSeek_R1.pdf), achieves performance comparable to OpenAI-o1 across math, code, and reasoning tasks. The model was trained via large-scale reinforcement learning without supervised fine-tuning as a preliminary step.

## Hardware Considerations

DeepSeek-R1 model way exceeds the memory capacity of a single GPU or even the entire DGX (NVIDIA's full-fledged AI machine). The model has 671 Billion parameters therefore it requires \~ 1.3 TB of vRAM only to host it's weights in an original format. It also has 128K tokens [context size](#user-content-fn-1)[^1] capacity, which bloats the memory requirements even further.

You could estimate your model vRAM requirements using any on-line calculator, for instance [ollama-gpu-calculator](https://aleibovici.github.io/ollama-gpu-calculator/).

However, for most of the use-cases it's more efficient to host a quantized version of the model that drastically reduces the required vRAM for weights and speeds-up the inference process slightly sacrificing a quality of results. Therefore, here we going to deploy an AWQ-quantized version of the DeepSeek-R1 model ([cognitivecomputations/DeepSeek-R1-AWQ](https://huggingface.co/cognitivecomputations/DeepSeek-R1-AWQ)). We are going to use two NVIDIA DGX nodes with 8 x NVIDIA Tesla H100 SMX cards (80 Gb of vRAM) within.

## Software Consideration

We are going to run vLLM inference server on top of the Apolo platform. As you know, Apolo simplifies deployment by handling resource allocation, workloads scheduling, orchestration & isolation of processes and provisioning ingress and authentication capabilities.

We are also going to leverage [Ray](https://docs.ray.io/en/latest/ray-overview/getting-started.html), a distributed computing framework that enables multi-GPU and multi-node inference to serve the model at a full context-length configuration, which is often required for the advanced tasks.

Here is a diagram overview of what we are trying to achieve:

<figure><img src="../.gitbook/assets/Apolo DeepSeek-R1 deployment diagram.svg" alt=""><figcaption><p>Apolo DeepSeek-R1 Ray deployment diagram</p></figcaption></figure>

Please note, if you don't have an access to such a resource preset, a generic guideline for building Ray cluster capable enough is to:

* Run all jobs on the same preset, so the hardware stays heterogeneous
* Set `--pipeline-parallel-size` to the number of jobs dedicated for your cluster, including the head job.
* Set `--tensor-parallel-size` to the number of GPUs in each job.
* The overall vRAM capacity should be around 1.2 Tb for inference on a full context length. You could reduce it by lowering the context length.

## Deploy with Apolo CLI

The easiest way to start is to utilize CLI.

Before going further, make sure you've completed the getting-started guide from the main documentation page, installed Apolo CLI, logged into the platform, created (or joined) organization and project.

Please note, the deployment consists of two main components: Ray head job and Ray worker job, which together form a static Ray cluster for hosting the vLLM server. Within a Ray worker node we will launch vLLM server that will be spread across the virtual cluster.

First, let's start a Ray head job:

{% code overflow="wrap" fullWidth="true" %}
```bash
apolo run --preset dgx --name ray-head --life-span 10d -v storage:hf-cache:/root/.cache/huggingface -e HF_TOKEN=secret:HF_TOKEN --entrypoint ray vllm/vllm-openai:v0.7.2 -- start --block --head --port=6379
```
{% endcode %}

Brief break down of the command:

* `-s dgx` specifies the preset hardware config name that will be used while running the job.
* `--life-span 10d` configures for how long should Apolo run the job.&#x20;
* `-v storage:hf-cache:/root/.cache/huggingface` mounts the platform storage folder `hf-cache` into the job's filesystem under /root/.cache/huggingface. This is where vLLM looks for model binaries on the startup.
* `--entrypoint ray`  overwrites a default entrypoint of the container image to `ray` executable.
* `vllm/vllm-openai`is a container image name, used to run the job.
* `start --block --head --port=6379` is a command arguments sent to `ray` executable.

After the scheduling, a Ray head job will start waiting for the incoming connections and serving the requests. Leave it for now running.

Before going further, note the output of the following command:

{% code fullWidth="false" %}
```
$ apolo status ray-head                                                                 
 Job                      job-324eab93-5fec-4cf6-bda5-3b6c701162bb                  
 Name                     ray-head  
...    
 Image                    vllm/vllm-openai:v0.7.2      
 Entrypoint               ray      
 Command                  start --block --head --port=6379 --dashboard-host=0.0.0.0 
 Preset                   dgx        
 Resources                 Memory                                2.1 TB             
                           CPU                                    220.0               
                           Nvidia GPU          8 x Nvidia-DGX-H100-80GB              
                           Extended SHM space                      True               
 Life span                10d                                   
 TTY                      True      
 Volumes                   /root/.cache/huggingface  storage:ollama_server/cache
 Internal Hostname        job-324eab93-5fec-4cf6-bda5-3b6c701162bb.platform-jobs
 Internal Hostname Named  ray-head--0e1436ea6a.platform-jobs <<=== THIS ONE! <====
 Http URL                 https://ray-head--0e1436ea6a.jobs.cl1.org.apolo.us 
 ...
```
{% endcode %}

It derives a runtime information from the running job on the platform. We are particularly interested in `Internal Hostname named` row. This is a domain name within the cluster, needed for the worker job to connect to the head.

Second, let's start a Ray worker node. Open a new console window and copy-paste the following command. Please parameterize the address of your Ray head job accordingly.

{% code overflow="wrap" fullWidth="true" %}
```bash
$ apolo run --preset dgx --http-port 8000 --no-http-auth --name ray-worker -v storage:hf-cache:/root/.cache/huggingface --entrypoint bash -e HF_TOKEN=secret:HF_TOKEN --life-span 10d vllm/vllm-openai:v0.7.2 \
-- -c '
ray start --address=ray-head--0e1436ea6a.platform-jobs:6379; python3 -m vllm.entrypoints.openai.api_server --model cognitivecomputations/DeepSeek-R1-AWQ --tokenizer cognitivecomputations/DeepSeek-R1-AWQ --dtype=float16 --tensor-parallel-size=8 --pipeline-parallel-size=2 --trust-remote-code --max-model-len=128000 --enforce-eager
'
```
{% endcode %}

This instructs Apolo to start Ray worker Job on another DGX machine. Within this job, we will also launch vLLM server on top of the running Ray cluster that will utilize a high-throughput intra-cluster communication (InfiniBand) protocol to serve a model distributed onto two machines. The startup process might take some time, depending on your cluster settings.

You will see the following line when the server is ready to accept requests:

```bash
...(other server logs)...
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### Query the model

When the model is up and running, you could use any standard client to query it. To derive the endpoint for sending the request, check the status of a ray-worker job:

<pre data-full-width="false"><code><strong>$ apolo status ray-worker
</strong> Job                      job-5cf69164-0f64-4d3f-8354-b4d02ee0c8f8                                                                                                                                                 
 Name                     ray-worker                                                                                                                                                                               
 ...
 Image                    vllm/vllm-openai:v0.7.2                                                                                                                                                                  
 Entrypoint               bash                                                                                                                                                                                     
 Command                  -c 'ray start --address=ray-head--0e1436ea6a.platform-jobs:6379                                                                                                                          
                            python3 -m vllm.entrypoints.openai.api_server --model cognitivecomputations/DeepSeek-R1-AWQ --tokenizer cognitivecomputations/DeepSeek-R1-AWQ --dtype=float16 --tensor-parallel-size=8 
                          --pipeline-parallel-size=2 --trust-remote-code --max-model-len=100000 --enforce-eager'                                                                                                   
 Preset                   dgx                                                                                                                                                                                      
 Resources                 Memory                                2.1 TB                                                                                                                                            
                           CPU                                    220.0                                                                                                                                            
                           Nvidia GPU          8 x Nvidia-DGX-H100-80GB                                                                                                                                            
                           Extended SHM space                      True                                                                                                                                            
 Life span                10d                                                                                                                                                                                      
 .TTY                      True                                                                                                                                                                                     
 Volumes                   /root/.cache/huggingface  storage:ollama_server/cache                                                                                                                                   
 Internal Hostname        job-5cf69164-0f64-4d3f-8354-b4d02ee0c8f8.platform-jobs                                                                                                                                   
 Internal Hostname Named  ray-worker--0e1436ea6a.platform-jobs                                                                                                                                                     
 Http URL                 https://ray-worker--0e1436ea6a.jobs.cl1.org.apolo.us &#x3C;&#x3C;=== THIS ONE! &#x3C;====
</code></pre>

There you are going to see  a public domain name of the job, at the `Http URL` row. Now, let's use `curl`to send the request:

```bash
curl -s https://ray-worker--0e1436ea6a.jobs.cl1.org.apolo.us/v1/completions \
    -H "Content-Type: application/json" \
    -d '{
        "model": "cognitivecomputations/DeepSeek-R1-AWQ",
        "prompt": "San Francisco is a",
        "max_tokens": 100,
        "temperature": 0
    }' | jq
{
  "id": "cmpl-ecb0b36cd86c454b95bf62a269e95848b3f9",
  "object": "text_completion",
  "created": 1739913613,
  "model": "cognitivecomputations/DeepSeek-R1-AWQ",
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

To cleanup hit ctrl+c in the console windows, where the jobs were launched. Alternatively, use cli command:

`apolo kill <job-id>`

to terminate the job.

This is it! You now have a fully operational DeepSeek R1 distributed model deployment on Apolo, serving inference requests efficiently with vLLM!

## Deploy with Apolo Flow

Apolo Flow allows one to template the workloads configuration into config files. The below configuration file snippet arranges the previously discussed deployment process in a couple of Apolo-Flow job descriptions. We also expand this scenario with the deployment of [OpenWebUI](https://openwebui.com/) server that acts as a web interface for chatting with your models.

{% code fullWidth="false" %}
```yaml
kind: live

defaults:
  life_span: 10d

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
      OPENAI_API_BASE_URL: http://${{ inspect_job('ray-worker').internal_hostname_named }}:8000/v1 # inspects vllm job in runtime to derive endpoint domain address

  ray_head:
    image: vllm/vllm-openai:v0.7.2
    name: ray-head
    preset: dgx
    detach: true
    life_span: 10d
    http_port: "8000"
    volumes:
      - ${{ volumes.hf_cache.ref_rw }}
    env:
      HF_TOKEN: secret:HF_TOKEN # your huggingface token used to pull models, could be added with `apolo secret add HF_TOKEN <token>`
    entrypoint: ray
    cmd: >
      start --block --head --port=6379 --dashboard-host=0.0.0.0

  ray_worker:
    image: vllm/vllm-openai:v0.7.2
    name: ray-worker
    preset: dgx
    detach: true
    http_port: "8000"
    http_auth: false
    life_span: 10d
    volumes:
      - ${{ volumes.hf_cache.ref_rw }}
    env:
      HF_TOKEN: secret:HF_TOKEN
    entrypoint: bash
    cmd: >
      -c 'ray start --address=${{ inspect_job('ray_head').internal_hostname_named }}:6379
        python3 -m vllm.entrypoints.openai.api_server --model cognitivecomputations/DeepSeek-R1-AWQ --tokenizer cognitivecomputations/DeepSeek-R1-AWQ --dtype=float16 --tensor-parallel-size=8 --pipeline-parallel-size=2 --trust-remote-code --max-model-len=100000 --enforce-eager'

```
{% endcode %}

You could find full configuration file in our GitHub [repository.](https://github.com/neuro-inc/mlops-open-webui)

Now start `ray_head`, `ray_worker` and `web` jobs. Please give Ray a minute to spin-up a Head job before continuing with the worker node.

```bash
apolo-flow run ray_head # wait a munite after running head job, so it starts
apolo-flow run ray_worker # it will connect to the ray_head
apolo-flow run web # this will attach to the worker node 
```

As in previous case, give vLLM some time to load such a huge LLM model into the GPU memory.

You can observe server startup process by connecting to the logs stream using cli command:

```bash
apolo-flow logs ray_worker
```

You will see the following line when the server is ready to accept requests:

```bash
...(other server logs)...
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
```

### Query the model

You could use any client that best fits your needs. You could also notice that a web page with OpenWebUI was opened in your default browser. There you could query your model:&#x20;

<figure><img src="../.gitbook/assets/deepseek-inference.gif" alt=""><figcaption><p>DeepSeek-R1 inference process</p></figcaption></figure>

That's it!\
Now you know how to spin-up the DeepSeek-R1 model using vLLM and exposed its interface via web browser using OpenWebUI web-server app.

Don't forget to terminate the launched jobs when you don't need it:

```bash
apolo-flow kill ALL
```

This will terminate all jobs launched in this flow.



[^1]: Read as a "memory" of the LLM, or its "inputs"
