# Running Your Code

Oftentimes you don't start a project from scratch. Instead of that you use someone's or your own old code as a baseline and develop your solution on top of it. This guide demonstrates how to take an existing code base, convert it into a Apolo flow, and start developing on the platform.

## Prerequisites

1. Make sure that you have the Apolo CLI [installed](getting-started.md#installing-cli) and logged in.
2. Install the `apolo-flow` package:

```bash
pip install -U apolo-flow
```

## Configuration

As an example we'll use the GitHub [repo](https://github.com/songyouwei/ABSA-PyTorch) that contains PyTorch implementations for Aspect-Based Sentiment Analysis models (see [Attentional Encoder Network for Targeted Sentiment Classification](https://paperswithcode.com/paper/attentional-encoder-network-for-targeted) for more details).

First, let's clone the repo and navigate to the created folder:

```bash
git clone https://github.com/songyouwei/ABSA-PyTorch.git
cd ABSA-PyTorch
```

Now, we need to create two more files in this folder:

* `Dockerfile` contains a very basic Docker image configuration. We need this file to build a custom Docker image which is based on `pytorch/pytorch` public images and contains this repo requirements (which are gracefully listed by the repo maintainer in `requirements.txt`). Since the repo was published, the Python ecosystem has moved on, so two adjustments are needed on top of the raw requirements: the deprecated `sklearn` PyPI package must be replaced with `scikit-learn` (installing `sklearn` now fails on purpose), and `protobuf` must be pinned below 4.0 (newer versions are incompatible with the `transformers` version this repo needs). Also note that we intentionally do not pass `-U` to pip: with it, the `torch>=0.4.0` requirement would replace the PyTorch already shipped in the base image with a several-gigabytes-larger fresh build.

{% code title="Dockerfile" %}
```bash
FROM pytorch/pytorch:1.4-cuda10.1-cudnn7-runtime
COPY . /cfg
RUN sed -i 's/^sklearn$/scikit-learn/' /cfg/requirements.txt && \
    pip install --progress-bar=off --no-cache-dir -r /cfg/requirements.txt "protobuf<3.21"
```
{% endcode %}

* `.apolo/live.yml` contains minimal configuration allowing us to run this repo's scripts right on the platform through handy short commands:

{% code title=".apolo/live.yml" %}
```yaml
kind: live
title: Sentiment Analysis Training
id: absa

volumes:
  project:
    remote: storage:${{ flow.flow_id }}
    mount: /project
    local: .

images:
  pytorch:
    ref: image:${{ flow.flow_id }}:v1.0
    dockerfile: ${{ flow.workspace }}/Dockerfile
    context: ${{ flow.workspace }}
    build_preset: cpu-large

jobs:
  train:
    image: ${{ images.pytorch.ref }}
    preset: cpu-large
    name: absa-pytorch-train
    volumes:
      - ${{ volumes.project.ref_rw }}
    bash: |
        cd ${{ volumes.project.mount }}
        python train.py --model_name bert_spc --dataset restaurant
```
{% endcode %}

{% hint style="info" %}
Resource presets are cluster-specific — list the ones available on your cluster with `apolo config show` and adjust the `preset` and `build_preset` values accordingly. The `build_preset` attribute matters here: the default build preset may not have enough memory for kaniko to snapshot the image layers with PyTorch dependencies.

Note on GPU presets: the `pytorch/pytorch:1.4-cuda10.1-cudnn7-runtime` image this 2020-era repo needs only supports pre-Ampere GPUs (CUDA 10.1 has no support for compute capability 8.0+), so on clusters with A100/L4/H100 GPUs this example is best run on a CPU preset. For GPU training with modern cards, start from a recent `pytorch/pytorch` image instead.
{% endhint %}

Here is a brief explanation of this config:

* `volumes` section contains declarations of connections between your computer file system and the platform storage; here we state that we want the entire project folder to be uploaded to storage at `storage:absa` folder and be mounted inside jobs `/project`;
* `images` section contains declarations of Docker images created in this project; here we declare our image which is decribed in `Dockerfile` above;
* `jobs` section is the one where action happens; here we declare a `train` job which runs our training script with a couple of parameters.

## Running code

Now it's time to run several commands that set up the project environment and run training.

* First, create volumes and upload project to platform storage:

```
apolo-flow mkvolumes
apolo-flow upload ALL
```

* Then, build an image:

```
apolo-flow build pytorch
```

{% hint style="info" %}
If you change the `Dockerfile` and need to rebuild, pass `-F` / `--force-overwrite` (`apolo-flow build -F pytorch`) — by default `apolo-flow` refuses to overwrite an image tag that already exists in the registry.
{% endhint %}

* Finally, run training:

```
apolo-flow run train
```

Please run `apolo-flow --help` to get more information about available commands.
