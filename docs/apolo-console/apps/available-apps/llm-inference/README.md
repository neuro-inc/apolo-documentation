# LLM Inference

[vLLM](https://github.com/vllm-project/vllm) is a high-performance and memory-efficient inference engine for large language models. It uses a novel GPU KV cache management strategy to serve transformer-based models at scale, supporting multiple GPUs (including NVIDIA and AMD) with ease. vLLM enables fast decoding and efficient memory utilization, making it suitable for production-level deployments of large LLMs.

#### Key Features

* **High Throughput Inference**: Novel GPU KV caching enables faster token generation compared to traditional implementations.
* **Multi-GPU Support**: Scales seamlessly to multiple GPUs, including AMD (MI200s, MI300s) and NVIDIA (V100/H100/A100) resource pools.
* **Easy Model Downloading**: Built-in integration with Hugging Face model repositories.
* **Flexible Configuration**: Control precision (`--dtype`), context window size, parallelism (`tensor-parallel-size`, `pipeline-parallel-size`), etc.
* **Lightweight & Extensible**: Minimal overhead for deployment and easy to integrate with existing MLOps or monitoring solutions.

#### Installation and Deployment on Apolo

You can deploy vLLM on the Apolo platform using the **`LLM Inference`** app. Apolo automates resource allocation, persistent storage, ingress, GPU detection, and environment variable injection, so you can focus on model configuration.

**Highlights of the Apolo Installation Flow**:

1. **Resource Allocation**: Choose an Apolo preset (e.g. `gpu-xlarge`, `mi210x2`) that specifies CPU, memory, and GPU resources.
2. **GPU Auto-Configuration**: If your preset includes multiple GPUs, environment variables (e.g. `CUDA_VISIBLE_DEVICES` or `HIP_VISIBLE_DEVICES`) are automatically set, along with a sensible default for parallelization.
3. **Ingress Setup**: Enable an ingress to expose vLLM’s HTTP endpoint for external access.
4. **Integration with Hugging Face**: You can pass your Hugging Face token via an environment variable to pull private models.

***

#### Parameter Descriptions

The following parameters can be set with Apolo’s CLI (`apolo run --pass-config ... install ... --set <key>=<value>`). Many are optional but can be used to customize your deployment:

| **Parameter**                     | **Type**  | **Description**                                                                                                                                   |
| --------------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **app\_name**                     | String    | Required. Name for your vLLM application (Kubernetes release name, etc.). Must follow Kubernetes naming rules. Example: `llm-inference qwen-32b`. |
| **preset\_name**                  | String    | Required. Apolo preset for resources. E.g. `gpu-xlarge`, `H100X1`, `mi210x2`. Sets CPU, memory, GPU count, and GPU provider.                      |
| **model.modelHFName**             | String    | Required. Hugging Face model repo name. Example: `Qwen/QwQ-32B-preview`.                                                                          |
| **model.modelRevision**           | String    | Optional. Specify the model revision/tag. Example: `main`.                                                                                        |
| **env.HUGGING\_FACE\_HUB\_TOKEN** | String    | Optional. HF token for private model downloads. If left blank, only public models are accessible.                                                 |
| **ingress.enabled**               | Boolean   | Optional (default: `false`). Enables external access to the vLLM HTTP endpoint with a Kubernetes Ingress. Example: `true`.                        |
| **ingress.clusterName**           | String    | Optional. The cluster name used in the generated domain. Example: `novoserve`. Produces `<app_name>.apps.novoserve.org.neu.ro`.                   |
| **serverExtraArgs**               | String\[] | Optional. Additional arguments passed to the `vllm serve` command (e.g. `--dtype=half`, `--max-model-len=131072`).                                |

Any additional chart values can also be provided through `--set` flags, but the above are the most common.

#### Example Apolo CLI Command

Below is a streamlined example command that deploys **vLLM** using the [`app-llm-inference` ](https://github.com/neuro-inc/app-llm-inference)app that deploys to a Nvidia preset:

```bash
apolo run --pass-config ghcr.io/neuro-inc/app-deployment -- \
  install https://github.com/neuro-inc/app-llm-inference \
  llm-inference vllm-large charts/llm-inference-app \
  --timeout=30m \
  --set "preset_name=H100X2" \
  --set "model.modelHFName=Qwen/QwQ-32B-preview" \
  --set "model.modelRevision=main" \
  --set "env.HUGGING_FACE_HUB_TOKEN=<YOUR_HF_TOKEN>" \
  --set "ingress.enabled=true" \
  --set "ingress.clusterName=<YOUR_CLUSTER_BAE>" \
  --set "serverExtraArgs[0]=--dtype=half" \
  --set "serverExtraArgs[1]=--max-model-len=131072" \
  --set "serverExtraArgs[2]=--enforce-eager"
```

Below is a streamlined example command that deploys **vLLM** using the [`app-llm-inference` ](https://github.com/neuro-inc/app-llm-inference)app that deploys to an AMD preset:

```bash
apolo run --pass-config ghcr.io/neuro-inc/app-deployment -- \
  install https://github.com/neuro-inc/app-llm-inference \
  llm-inference vllm-large charts/llm-inference-app \
  --timeout=30m \
  --set "preset_name=mi210x2" \
  --set "model.modelHFName=Qwen/QwQ-32B-preview" \
  --set "model.modelRevision=main" \
  --set "env.HUGGING_FACE_HUB_TOKEN=<YOUR_HF_TOKEN>" \
  --set "ingress.enabled=true" \
  --set "ingress.clusterName=<YOUR_CLUSTER_BAE>" \
  --set "serverExtraArgs[0]=--dtype=half" \
  --set "serverExtraArgs[1]=--max-model-len=131072" \
  --set "serverExtraArgs[2]=--enforce-eager"
```

**Explanation**:

* **`preset_name=gpu-xlarge`** requests 2 GPUs (NVIDIA H100). Apolo automatically sets `CUDA_VISIBLE_DEVICES=0,1` and default parallelization flags unless overridden.
* **`preset_name=mi210x2`** requests 2 GPUs (AMD MI210). Apolo automatically sets `HIP_VISIBLE_DEVICES=0,1` , `ROCR_VISIBLE_DEVICES=0,1`  and default parallelization flags unless overridden.
* **`model.modelHFName=Qwen/QwQ-32B-preview`**: The Hugging Face model to load.
* **`ingress.enabled=true`** & **`ingress.clusterName=<YOUR_CLUSTER_NAME>`**: Creates a public domain (e.g. `vllm-large.apps.<YOUR_CLUSTER_NAME>.org.neu.ro`) pointing to the vLLM deployment.
* **`serverExtraArgs[...]`**: Additional flags (`--dtype=half`, `--max-model-len=131072`, etc.) are appended to the `vllm serve` command.

#### References

* [vLLM Official GitHub Repo](https://github.com/vllm-project/vllm)
* [app-llm-inference Helm Chart Repository](https://github.com/neuro-inc/app-llm-inference)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
* [Hugging Face Model Hub](https://huggingface.co/) (for discovering or hosting models)

