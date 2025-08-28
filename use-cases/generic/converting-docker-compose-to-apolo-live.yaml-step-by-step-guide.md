# Converting docker-compose to Apolo live.yaml - step by step guide

## Overview <a href="#j8bc1l6kdrt6" id="j8bc1l6kdrt6"></a>

This document’s aim is to guide the user on how to approach conversion of their existing docker-compose.yaml that launches an application or service to Apolo using Apolo Flow. Live mode of Apolo Flow (live.yaml configuration file) is a similar concept allowing to launch applications consisting of several “jobs” on Apolo platform. The document takes a sample docker-compose.yaml that launches privateGPT wih a model served by vLLM along with miscellanea and shows how to adapt it to Apolo live.yaml that serves a similar function.

## Concepts: <a href="#ddy9mprozem7" id="ddy9mprozem7"></a>

#### Apolo Workflows: <a href="#id-9m4j1fvzlxcs" id="id-9m4j1fvzlxcs"></a>

* A workflow is a configurable automated process made up of one or more job, task, or action calls. You must create a YAML file to define your workflow configuration.
* Workflow kinds - there are two kinds of workflows: live and batch. We will discuss live workflow in this document.
* Live workflows - ( [Live workflow syntax](https://app.gitbook.com/s/-MMLOF_FqiWBMcOdY8cj/workflow-syntax/live-workflow-syntax "mention") ) workflows are controlled from the developer's machine. They contain a set of job definitions that spawn jobs in the Apolo cloud.

#### Here's an example of a typical Apolo flow job: <a href="#id-9m4j1fvzlxcs" id="id-9m4j1fvzlxcs"></a>

1. Executing a Jupyter Notebook server in the cloud on a powerful node with a lot of memory and a high-performant GPU.
2. Opening a browser with a Jupyter web client connected to this server.

#### Docker-compose: <a href="#id-8pentobz7x12" id="id-8pentobz7x12"></a>

* Docker Compose is a tool for defining and running multi-container applications. It is the key to unlocking a streamlined and efficient development and deployment experience.

#### Comparison table between Docker Compose and Apolo Flow Live mode <a href="#seuli4re9wg8" id="seuli4re9wg8"></a>

| **Criteria**        | **Docker Compose**                                | **Apolo Flow Live Mode**                         |
| ------------------- | ------------------------------------------------- | ------------------------------------------------ |
| **Driver**          | Local Docker Engine                               | Apolo Cloud                                      |
| **DSL**             | Docker Compose Link (YAML configuration)          | Apolo Flow Link (YAML configuration)             |
| **Templating**      | Inheritance                                       | Modules & Mixins                                 |
| **Images**          | Local build & storage                             | Apolo Cloud (built and stored in the cloud)      |
| **Storage**         | Local storage (volumes)                           | Apolo Cloud (persistent storage in the cloud)    |
| **Networking**      | Local network / machine                           | Apolo Project (internal service discovery)       |
| **Service Scaling** | Manual or external tools                          | Limited direct scaling (unless using Apolo Apps) |
| **Collaboration**   | Primarily local, manual sharing of configurations | Built-in collaboration features within Apolo     |

#### **Docker-Compose Service Architecture** <a href="#ytoacorj2461" id="ytoacorj2461"></a>

Please note that the examples use different model serving (ollama vs. vLLM and traefik configs). We will address this in the document.

Let’s first look at Docker-Compose file and its structure:

This is a Docker Compose configuration for PrivateGPT with multiple deployment options. Here's a breakdown of the architecture and services:

#### **PrivateGPT Services** <a href="#bfyfhq7rfsig" id="bfyfhq7rfsig"></a>

**private-gpt-ollama**

* Main application service that interfaces with Ollama
* Runs on port 8001 with Docker profile configuration
* Supports CPU, CUDA, and API modes through profiles
* Depends on Ollama service for model inference

**private-gpt-llamacpp-cpu**

* Alternative deployment using llama.cpp backend
* CPU-only inference with local model storage
* Direct model execution without external dependencies
* Uses local profile for standalone operation

#### **Ollama Infrastructure** <a href="#e27g4nvvpcw8" id="e27g4nvvpcw8"></a>

**ollama (Traefik Proxy)**

* Reverse proxy using Traefik v2.10
* Routes requests to appropriate Ollama backends
* Health checks ensure service availability
* Exposes management interface on port 8080

**ollama-cpu**

* CPU-based Ollama service
* Standard deployment for systems without GPU
* Model storage in local ./models directory

**ollama-cuda**

* GPU-accelerated Ollama service
* Requires NVIDIA GPU with CUDA support
* Uses Docker GPU resource reservations
* Same model storage as CPU version

### **Key Features** <a href="#id-4exn33qlzbw9" id="id-4exn33qlzbw9"></a>

**Profile-Based Deployment**

* Multiple profiles: ollama-cpu, ollama-cuda, ollama-api, llamacpp-cpu
* Flexible deployment options based on hardware capabilities
* Easy switching between inference backends

**Model Management**

* Shared model storage in ./models directory
* Persistent data storage in ./local\_data
* Hugging Face integration with token support

**Service Discovery**

* Traefik handles routing and load balancing
* Health checks ensure service reliability
* Internal networking between services

This setup provides a comprehensive private AI deployment with options for both CPU and GPU inference, making it suitable for various hardware configurations and use cases.

Now let’s look at how live.yaml is structured:

This is Apolo configuration file for deploying a private GPT system with multiple components. Here's what this setup provides:

### **Architecture Overview** <a href="#qnub70w61u92" id="qnub70w61u92"></a>

The configuration defines a distributed system with four main services:

**Core Application (pgpt) -** PrivateGPT is a production-ready AI project that allows you to ask questions about your documents using the power of Large Language Models (LLMs), even in scenarios without an Internet connection. 100% private, no data leaves your execution environment at any point.

* Runs the main PrivateGPT application on CPU resources
* Serves the web interface on port 8080
* Uses profiles for app and pgvector integration
* Connects to external VLLM and embedding services

**Language Model Service (vllm) -** vLLM is an open-source library designed to make Large Language Model (LLM) inference faster and more efficient, especially in production settings. It achieves this by using an innovative memory management system called PagedAttention, which optimizes GPU memory for the LLM's [KV cache](https://www.google.com/search?sca_esv=b4e332f294b324c3\&q=KV+cache\&sa=X\&ved=2ahUKEwjpncLZgauPAxXHYPEDHeyVA2QQxccNegQIGBAB\&mstk=AUtExfCpuSTh5YN2k9OSROQZaD9Bbg7wdH1984xDTRO77uAxF0QPlcaJgdHcg2fJFeskfDu44bR4OZbn8DtrJgnR3JLxvgPnSzKXHv87BlfHfIlhSXiJkeK-FR3tLnjlyo3gS6s6gLjzNsA0Rm4opyP4V_-K7y-Viucv2fYyc0_YpO7aOIeEaIq7A4OV4VtpVnpNwOP1_GdEgIe-Epcq6ZJa7BwZR2rTRskZexd4M-FeMpeZLQ\&csui=3). vLLM also uses techniques like continuous batching to process requests dynamically, improving throughput and reducing latency.

* Deploys DeepSeek-R1-Distill-Qwen-32B model using VLLM
* Runs on A100 GPU for high-performance inference
* Provides OpenAI-compatible API endpoints
* Uses half-precision (float16) for memory efficiency

**Embedding Service (tei) -** Text Embeddings Inference (TEI) is a comprehensive toolkit designed for efficient deployment and serving of open source text embeddings models. It enables high-performance extraction for the most popular models, including FlagEmbedding, Ember, GTE, and E5.

TEI offers multiple features tailored to optimize the deployment process and enhance overall performance.

* Handles text embeddings using Nomic's embedding model
* Also runs on A100 GPU for fast embedding generation
* Supports batch processing up to 100 clients

**Database (pgvector) -** a PostgreSQL extension that provides powerful functionalities for working with high-dimensional vectors. It introduces a dedicated data type, operators, and functions that enable efficient storage, manipulation, and analysis of vector data directly within the PostgreSQL database.

* PostgreSQL with vector extension for similarity search
* Stores document embeddings and metadata
* Uses persistent storage for data durability

### **Key Features** <a href="#l6dkrz3qbog8" id="l6dkrz3qbog8"></a>

* **Persistent Storage**: Multiple volumes for caching, data, and settings
* **GPU Optimization**: Uses A100 GPUs for both LLM and embedding inference
* **Service Discovery**: Jobs communicate via internal hostnames
* **Security**: Uses Hugging Face tokens stored as secrets
* **Scalability**: Configurable context window (34K tokens) and batch processing

This setup is ideal for organizations wanting to run a private, on-premises ChatGPT-like system with document ingestion capabilities while maintaining data privacy and control.

## Converting docker-compose to live.yaml: <a href="#wcrvvcie174m" id="wcrvvcie174m"></a>

#### 1 . Study the docker‑compose architecture

1. **Identify services, images and profiles.** In the provided Compose file, PrivateGPT defines two services for the application (one using Ollama and another using llama.cpp), a Traefik reverse‑proxy and Ollama services for CPU and GPU. For example, the `private-gpt-ollama` service builds the `Dockerfile.ollama`, exposes port 8001 and sets environment variables such as `PGPT_MODE=ollama` and `PGPT_OLLAMA_API_BASE=http://ollama:11434` . Profiles (`ollama-cpu`, `ollama-cuda`, `ollama-api`, etc.) select which Ollama backend to use.
2. **Observe volumes.** Compose mounts local folders (e.g., `./local_data` to `/home/worker/app/local_data` and `./models` to `/root/.ollama` for Ollama services) for data persistence.
3. **Notice dependencies and networking.** Compose uses `depends_on` to start the Ollama proxy before PrivateGPT. Port mappings (`ports`) expose containers to the host. Traefik routes requests to the appropriate Ollama backend.

Understanding these relationships will help you map each piece into Apolo concepts.

In Apolo’s flow configuration, variables like `project` and `flow` come from the **context** that the platform injects when it runs a workflow. They let you reference the current project or workflow without hard‑coding identifiers or paths.

#### What `project` refers to

* **`project`** represents the Apolo project that owns your flow. A project is the top‑level container where you store jobs, images, secrets and flows. Projects make it easy to organize resources and share them with teammates.&#x20;
* **`project.id`** returns the unique identifier of the current project. You use this in image names (`image:$[[ project.id ]]:v1`) and storage paths (`storage:$[[ flow.project_id ]]/data`), so that the resources are namespaced correctly.
* Other attributes include `project.name` (the human‑readable name) and `project.owner`. These can be used in more advanced flows for templating or logging, but `project.id` is the most common.

#### What `flow` refers to

* **`flow`** represents the workflow instance being executed. It provides information about the run environment.
* **`flow.workspace`** is the path to the workspace directory on the build machine. When building images, Apolo mounts your repository at this location. In the PrivateGPT live example the `dockerfile` and `context` fields point to `$[[ flow.workspace ]]/Dockerfile.apolo` and `$[[ flow.workspace ]]/`, telling Apolo to build the image from the repository root.
* **`flow.project_id`** is equivalent to `project.id`, but scoped to the running flow. It appears in storage definitions such as `remote: storage:$[[ flow.project_id ]]/data`, ensuring that volumes are created within the current project’s namespace.

#### Where to find more information

* The **Apolo documentation** describes the platform as a “flexible and robust machine learning platform” and directs users to sign up and explore its features. This is a good starting point for understanding projects, flows and other core concepts.
* Within the docs, look under the **“Flow CLI”** and **“Flow syntax”** sections for a detailed explanation of context variables like `project` and `flow`, and examples showing how to use them in `live.yaml`. These sections are accessible through the navigation on the [Apolo documentation site](https://docs.apolo.us/index).

In short, `project` and `flow` are context objects that Apolo makes available to your YAML files so you can write generic configurations. `project.id` and `flow.project_id` let you create images and volumes inside the current project, while `flow.workspace` tells Apolo where to find your source code when building images.

#### 2 . Create the Apolo project skeleton

1.  **Set the workflow kind and metadata.** A live workflow runs on Apolo’s cloud but uses the developer’s machine as the entry point. Start with:

    ```yaml
    kind: live
    title: private-gpt-ollama
    defaults:
      life_span: 7d
      env:
        # global environment variables extracted from Compose
        PGPT_TAG: "0.6.2"
        HF_TOKEN: secret:HF_TOKEN
    ```

    The `life_span` controls how long the jobs stay running, and `env` defines variables available to all jobs.
2.  **Define images.** Compose either builds images locally or pulls them from Docker Hub. In Apolo you must define images explicitly under an `images` section. For example:

    ```yaml
    images:
      private-gpt-ollama:
        ref: image:$[[ project.id ]]:pgpt-ollama-v1
        dockerfile: Dockerfile.ollama
        context: $[[ flow.workspace ]]/ 
        build_preset: cpu-large
    ```

    Similarly define an image for the llama.cpp version if needed. `build_preset` chooses CPU or GPU resources for the build.

#### 3 . Translate volumes

Compose volumes become Apolo volumes backed by persistent storage. For each local directory in Compose, define a volume:

```yaml
volumes:
  local_data:
    remote: storage:$[[ flow.project_id ]]/local_data
    mount: /home/worker/app/local_data
    local: local_data
  models:
    remote: storage:$[[ flow.project_id ]]/models
    mount: /home/worker/app/models
    local: models
  ollama_models:
    remote: storage:$[[ flow.project_id ]]/ollama_models
    mount: /root/.ollama
    local: models
```

The `remote` path stores data in Apolo’s persistent storage. `mount` specifies where the volume is mounted inside the container, and `local` points to a folder on your development machine if you want to sync content.

In Apolo, a **volume** definition tells the platform how to mount persistent storage into your job containers. It has three main fields:

* **`remote`** – the path in Apolo’s object‑storage service where the data will be stored. You usually prefix it with `storage:$[[ flow.project_id ]]` so that it lives under the current project’s namespace. In the PrivateGPT live.yaml, for example, the `cache` volume points to `storage:$[[ flow.project_id ]]/cache`. Using a unique remote path ensures the data persists across job restarts.
* **`mount`** – the directory inside the container where the volume will be mounted. For the `cache` volume, Apolo mounts it at `/root/.cache/huggingface`so that Hugging Face downloads are written to persistent storage instead of the container’s temporary filesystem. When multiple jobs reference the same volume, they all see the same files under their `mount` path.
* **`local`** – an optional local directory on your machine that is synchronised with the remote storage. This is useful in live workflows because you can edit files locally and have them appear inside the container. In the example, `local: cache` means Apolo will sync the remote cache bucket with a `cache/` folder in your working directory.If you omit `local`, the volume still persists in the cloud but there’s no local sync.

Here’s how these fields come together for the different volumes in the PrivateGPT live workflow:

<table><thead><tr><th width="140.5625">Volume</th><th width="288.34765625">Purpose and mount point</th><th>Description</th></tr></thead><tbody><tr><td><code>cache</code></td><td><code>/root/.cache/huggingface</code></td><td>Stores model and tokenizer downloads from Hugging Face. The remote path <code>storage:$[[ flow.project_id ]]/cache</code> persists the cache across runs, and <code>local: cache</code> syncs it with a local <code>cache/</code> folder.</td></tr><tr><td><code>data</code></td><td><code>/home/worker/app/local_data</code></td><td>Holds the application’s working data (documents you upload to PrivateGPT). Persisting this directory allows state to survive job restarts.</td></tr><tr><td><code>pgdata</code></td><td><code>/var/lib/postgresql/data</code></td><td>Stores PostgreSQL data files for the <code>pgvector</code> job. Using a remote volume prevents database data loss if the job stops.</td></tr><tr><td><code>settings</code></td><td><code>/home/worker/app/settings</code></td><td>Contains configuration files for PrivateGPT; syncing this directory lets you update settings locally and have them applied in the container.</td></tr><tr><td><code>tiktoken_cache</code></td><td><code>/home/worker/app/tiktoken_cache</code></td><td>Caches tokenization data used by the tokenizer library. This avoids re‑downloading or re‑computing tokenization information on every run.</td></tr></tbody></table>

By defining volumes in this way, you achieve the same persistent‑storage behaviour as Docker Compose’s bind mounts, but with the added ability to sync files from your local machine and store them in Apolo’s cloud

#### 4 . Convert services into jobs

Apolo doesn’t run containers directly; it launches **jobs**. Each Compose service should become a job with analogous settings:

**Main application jobs**

*   **Ollama mode job**: Translate the `private-gpt-ollama` service into a job:

    ```yaml
    jobs:
      pgpt-ollama:
        image: ${{ images.private-gpt-ollama.ref }}
        name: pgpt-ollama
        preset: cpu-small        # choose an Apolo resource preset
        http_port: "8001"
        detach: true             # keep running after the workflow finishes
        browse: true             # enable remote browsing
        volumes:
          - ${{ volumes.local_data.ref_rw }}
        env:
          PORT: 8001
          PGPT_PROFILES: docker
          PGPT_MODE: ollama
          PGPT_EMBED_MODE: ollama
          PGPT_OLLAMA_API_BASE: http://${{ inspect_job('ollama-proxy').internal_hostname_named }}:11434
          HF_TOKEN: secret:HF_TOKEN
    ```
*   **llama.cpp mode job**: Convert `private-gpt-llamacpp-cpu` into another job:

    ```yaml
      pgpt-llamacpp:
        image: ${{ images.private-gpt-llamacpp.ref }}
        name: pgpt-llamacpp
        preset: cpu-medium
        http_port: "8001"
        detach: true
        browse: true
        volumes:
          - ${{ volumes.local_data.ref_rw }}
          - ${{ volumes.models.ref_rw }}
        env:
          PORT: 8001
          PGPT_PROFILES: local
          HF_TOKEN: secret:HF_TOKEN
        cmd: >
          sh -c ".venv/bin/python scripts/setup && .venv/bin/python -m private_gpt"
    ```

    Note that the Compose entrypoint (which runs a shell script) becomes the `cmd` field in Apolo.

**Backend services**

*   **Ollama CPU/GPU back‑ends**: Compose defines `ollama-cpu` and `ollama-cuda` using the `ollama/ollama:latest` image. In Apolo:

    ```yaml
      ollama-cpu:
        image: ollama/ollama:latest
        name: ollama-cpu
        preset: cpu-large
        detach: true
        port_forward:
          - "11434:11434"
        volumes:
          - ${{ volumes.ollama_models.ref_rw }}

      ollama-cuda:
        image: ollama/ollama:latest
        name: ollama-cuda
        preset: gpu-k80-small     # select an Apolo GPU preset
        detach: true
        port_forward:
          - "11434:11434"
        volumes:
          - ${{ volumes.ollama_models.ref_rw }}
    ```
*   **Reverse proxy replacement**: Compose uses Traefik to route to either CPU or GPU Ollama. Apolo has built‑in service discovery, so you can replace Traefik with a simple Nginx‑based proxy or reference the back‑end jobs directly. For instance:

    ```yaml
      ollama-proxy:
        image: nginx:alpine
        name: ollama-proxy
        preset: cpu-small
        detach: true
        port_forward:
          - "11434:11434"
        volumes:
          - ${{ upload('.docker/nginx.conf').ref }}:/etc/nginx/nginx.conf:ro
    ```

    The Nginx configuration would route `/v1/*` to the CPU or GPU Ollama job. Alternatively, skip the proxy and set `PGPT_OLLAMA_API_BASE` to the internal hostname of the desired job (see Step 7).

**Additional services**

The original Apolo example uses **vLLM**, **text‑embeddings‑inference** and **pgvector** instead of Ollama. If you choose those back‑ends, define jobs as in the live.yaml: `vllm` runs the LLM on an A100 GPU and sets model parameters; `tei` runs the text embedding service; and `pgvector` sets up a PostgreSQL database with persistent storage.

#### 5 . Handle dependencies and service discovery

Docker Compose’s `depends_on` ensures one service starts after another. In Apolo, jobs don’t block each other; instead, the application should wait for dependencies internally or specify environment variables using Apolo’s `inspect_job` helper:

```yaml
PGPT_OLLAMA_API_BASE: http://${{ inspect_job('ollama-cpu').internal_hostname_named }}:11434
```

`inspect_job('job-name').internal_hostname_named` returns the internal hostname of another job so services can communicate without exposing ports.

If your app needs a delay before connecting, implement retry logic in your startup script or set environment variables such as `OLLAMA_STARTUP_DELAY`.

#### 6 . Manage secrets and environment variables

*   Replace sensitive variables (e.g., `HF_TOKEN` in Compose) with Apolo secrets:

    ```yaml
    HF_TOKEN: secret:HF_TOKEN
    ```
* Include other environment variables from Compose (e.g., `PGPT_MODE`, `PGPT_EMBED_MODE`, `PORT`) in the job’s `env` section.

#### 7 . Support multiple deployment profiles

Compose uses profiles (`ollama-cpu`, `ollama-cuda`, `llamacpp-cpu`) to choose back‑ends. In Apolo you can create separate live configuration files that extend the base workflow and override specific jobs. For example:

**live‑ollama‑cpu.yaml**

```yaml
extends: live.yaml
jobs:
  pgpt-ollama:
    env:
      PGPT_OLLAMA_API_BASE: http://${{ inspect_job('ollama-cpu').internal_hostname_named }}:11434
  ollama-cpu: ${parent.jobs.ollama-cpu}
```

**live‑ollama‑cuda.yaml**

```yaml
extends: live.yaml
jobs:
  pgpt-ollama:
    env:
      PGPT_OLLAMA_API_BASE: http://${{ inspect_job('ollama-cuda').internal_hostname_named }}:11434
  ollama-cuda: ${parent.jobs.ollama-cuda}
```

These override the API base URL and include only the desired backend job.

#### 8 . Validate and iterate

* **Test your live workflow locally** by running `apolo flow run` to ensure jobs start successfully and that the PrivateGPT application can communicate with the back‑end.
* **Adjust resource presets** based on the performance of each job (CPU vs GPU).
* **Use Apolo’s built‑in service discovery** to simplify networking; avoid exposing ports unless the service must be accessible outside the workflow.

***

By following this process, you convert the container‑oriented Docker Compose setup into an Apolo live workflow. Each Compose service becomes an Apolo job with corresponding images, volumes and environment variables; Compose profiles map to separate flow files; and networking is handled through Apolo’s internal hostnames and optional proxies. The end result retains the original functionality while taking advantage of Apolo’s cloud‑native features such as persistent storage, GPU presets and collaborative workflows.

## Conversions Notes

Apolo’s live workflow doesn’t need to be a literal mirror of your Compose file—it’s meant to express the same application architecture in a job‑centric, cloud‑native way. In the sample live.yaml we looked at, the model is served by **vLLM** and embeddings by **text‑embeddings‑inference**, so the only jobs defined are `pgpt`, `vllm`, `tei` and `pgvector`. There is no Ollama backend or Traefik job because:

* **The model and embedding services are different.** The Apolo example switches from Ollama to vLLM (`vllm/vllm-openai:v0.6.6.post1`) and from Traefik to direct service discovery. If you use this architecture, there is no need to run `ollama-cpu` or `ollama-cuda` jobs.
* **Apolo has built‑in service discovery.** Jobs communicate by referencing each other’s internal hostnames (`inspect_job('vllm').internal_hostname_named`), so a reverse proxy like Traefik—used in Compose to route requests to Ollama—is unnecessary.

If you want to keep using **Ollama** as your language‑model backend, you can certainly add `ollama-cpu` and/or `ollama-cuda` jobs, as shown in the conversion steps. Each would have its own image (`ollama/ollama:latest`), resource preset and shared volume for model storage. You’d then set `PGPT_OLLAMA_API_BASE` in your application job to point to the chosen Ollama job’s internal hostname instead of vLLM. In that case you still don’t need Traefik, because Apolo’s service discovery lets you route requests directly, or you can use a simple Nginx proxy if you prefer.

So, you only add `ollama-cpu`, `ollama-cuda` or a proxy job when you explicitly choose to deploy the Ollama backend on Apolo; they’re not part of the default live.yaml that uses vLLM.

### Spinning up and shutting down the live workflow

Once your `live.yaml` file is ready and you’ve defined any required images and secrets, you can start and stop your PrivateGPT deployment using the **Apolo CLI**. The `APOLO.md` in the PrivateGPT repository outlines a typical workflow for the vLLM‑based setup; the same pattern applies when using Ollama or other back‑ends.

**1. Build your images and set secrets**

1.  **Clone the repository** and move into it:

    ```bash
    git clone <repo-url>
    cd private-gpt
    ```
2.  **Build the custom image** defined in your `images` section. For the vLLM example the image is called `privategpt`; use `apolo-flow build` to build it:

    ```bash
    tapolo-flow build privategpt
    ```
3.  **Create required secrets.** For example, create a secret for your Hugging Face token:

    ```bash
    apolo secret add HF_TOKEN <your-hf-token>
    ```

    Secrets can then be referenced in your YAML using `secret:HF_TOKEN`.

**2. Start the jobs**

Apolo lets you run either the entire live workflow or individual jobs:

*   **Run individual jobs.** In `APOLO.md` the vector store, embedding service, LLM server and web application are started separately with `apolo-flow run`:

    ```bash
    apolo-flow run pgvector    # start PostgreSQL with pgvector extension
    apolo-flow run tei        # start the embedding server
    apolo-flow run vllm       # start the language model service
    apolo-flow run pgpt       # start the PrivateGPT web server
    ```

    Each command reads the definition for that job from `live.yaml`, creates the necessary volumes and schedules the job in Apolo’s cloud. You can adapt this pattern for your Ollama‑based jobs (`pgpt-ollama`, `ollama-cpu`, `ollama-cuda`, etc.).

During execution you can use `apolo job ls` to see running jobs and `apolo job logs <job-name>` to inspect their logs.

**3. Shut down the workflow**

To stop a running job, use the **stop** sub‑command:

```bash
apolo-flow stop pgpt        # stop the PrivateGPT job
apolo-flow stop vllm        # stop the vLLM job
apolo-flow stop tei         # stop the embedding job
apolo-flow stop pgvector    # stop the database
```

Stopping a job removes the container but **does not delete the volumes**, so your data in the `storage:$[[ flow.project_id ]]` buckets remains intact. You can restart the jobs later with `apolo-flow run <job>` and they will pick up where they left off. To completely clean up, delete the volumes or buckets via the Apolo console or CLI (`apolo storage rm <path>`).

Alternatively, you can manage jobs from the **Apolo console**: navigate to the _Jobs_ section, select a job and click **Stop** to terminate it. Use **Start** to relaunch a stopped job or **Delete** to remove it entirely.

By following these commands, you can reliably bring up your PrivateGPT environment on Apolo, perform your work, and then shut it down when you’re finished—all while keeping your data safe in persistent storage.

## Resources:

[Docker compose example in Apolo Github](https://github.com/neuro-inc/private-gpt/blob/apolo/docker-compose.yaml)

[Apolo flow example in the Apolo Github](https://github.com/neuro-inc/private-gpt/blob/apolo/.apolo/live.yaml)

[Docker-compose manual](https://docs.docker.com/compose/)

[Apolo flow documentation](https://app.gitbook.com/s/-MMLOF_FqiWBMcOdY8cj/)

