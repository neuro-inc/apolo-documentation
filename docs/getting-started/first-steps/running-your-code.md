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
git clone git@github.com:songyouwei/ABSA-PyTorch.git
cd ABSA-PyTorch
```

Now, we need to create two more files in this folder:

* `Dockerfile` contains a very basic Docker image configuration. We need this file to build a custom Docker image which is based on `pytorch/pytorch` public images and contains this repo requirements (which are gracefully listed by the repo maintainer in `requirements.txt`):

{% code title="Dockerfile" %}
```bash
FROM pytorch/pytorch:1.4-cuda10.1-cudnn7-runtime
COPY . /cfg
RUN pip install --progress-bar=off -U --no-cache-dir -r /cfg/requirements.txt
```
{% endcode %}

* `.neuro/live.yml` contains minimal configuration allowing us to run this repo's scripts right on the platform through handy short commands:

{% code title=".neuro/live.yml" %}
```yaml
kind: live
title: Sentiment Analysis Training
id: absa

volumes:
  project:
    remote: storage:${{ flow.id }}
    mount: /project
    local: .

images:
  pytorch:
    ref: image:${{ flow.id }}:v1.0
    dockerfile: ${{ flow.workspace }}/Dockerfile
    context: ${{ flow.workspace }}

jobs:
  train:
    image: ${{ images.pytorch.ref }}
    preset: gpu-small
    name: absa-pytorch-train
    volumes:
      - ${{ volumes.project.ref_rw }}
    bash: |
        cd ${{ volumes.project.mount }}
        python train.py --model_name bert_spc --dataset restaurant
```
{% endcode %}

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

* Finally, run training:

```
apolo-flow run train
```

Please run `apolo-flow --help` to get more information about available commands.
