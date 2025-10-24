# Apolo Base Docker image

Our company provides a public Docker image with pre-configured **Conda environments** and pre-installed **Python dependencies**, designed to simplify development and deployment. This image ensures compatibility, consistency, and efficiency across diverse workflows, making it an ideal solution for streamlined integration into your projects.

Explore the guide below for setup instructions and configuration details.

## Github repo

The **GitHub repository** serves as the primary source of truth for all updates, configurations, and detailed documentation regarding this Docker image.

[https://github.com/neuro-inc/apolo-base-image](https://github.com/neuro-inc/apolo-base-image)

### Releases

Releases can be found in [Releases github tab](https://github.com/neuro-inc/apolo-base-image/releases)

Each release includes four Docker images, each configured with a specific set of dependencies.

Dependencies version can be found in the specific release page. [Example](https://github.com/neuro-inc/apolo-base-image/releases/tag/v24.12.0)

## Usage

You can utilize our docker image in various ways, pulling it from public repository, using it locally or in Apolo jobs.

**Base path**

```
ghcr.io/neuro-inc/base
```

**Non-versioned tags**

```
latest-runtime-minimal
latest
latest-runtime
latest-devel
latest-devel-minimal
```

**Apolo-flow**

when you are creating Apolo flow using our template, by default we expose base Docker

{% embed url="https://github.com/neuro-inc/flow-template" %}
Apolo flow repo
{% endembed %}

{% embed url="https://github.com/neuro-inc/flow-template-barebone" %}
Apolo flow barebone
{% endembed %}

Also, if you don't want to edit the Dockerfile, you can specify docker directly in your job .neuro/live.yml

```
......... .neuro/live.yml

kind: live
title: My flow
defaults:
   .......
jobs:
  job:
    args:
      image: ghcr.io/neuro-inc/base:latest
```

## Conda environments

By default, our Docker image provides three Conda environments:

*   **Default**: Automatically activated through the `.bashrc` configuration for all terminal sessions

    can be activated using

    <pre><code><strong>conda activate base 
    </strong>OR
    <strong>source /opt/conda/bin/activate base
    </strong></code></pre>
*   **TensorFlow-specific**: Optimized for TensorFlow workflows.\
    can be activated using

    <pre><code><strong>conda activate tf 
    </strong>OR
    <strong>source /opt/conda/bin/activate tf
    </strong></code></pre>
*   **Torch-specific**: Tailored for PyTorch operations.

    can be activated using

    <pre><code><strong>conda activate torch 
    </strong>OR
    <strong>source /opt/conda/bin/activate torch
    </strong></code></pre>

**Environment**

We strive to keep dependencies up-to-date. If you require a more recent version or believe an update could enhance the platform, please reach out to us. Alternatively, you can extend our base Dockerfile to install any additional dependencies you need.

```
FROM ghcr.io/neuro-inc/base:v24.12.0-runtime
RUN pip install ...
```

## References

* [Base Docker repository](https://github.com/neuro-inc/neuro-base-environment/tree/master)
* [Base Apolo flow template using the latest Docker](https://github.com/neuro-inc/flow-template)
