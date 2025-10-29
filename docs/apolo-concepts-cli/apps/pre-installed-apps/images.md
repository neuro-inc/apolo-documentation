# Images

## Overview

The Images app provides a central location for viewing properties of container image assets used in deployments. For more information about the images App and how to use it in Apolo Console, visit the main [Images app](../../../apolo-console/apps/pre-installed/images.md) page.

## **Command Line Interface**

While the Images app provides a graphical interface for viewing image properties, the Apolo CLI offers powerful commands for managing container images directly from your terminal. The `apolo image` command suite includes the following operations:

### **Basic Commands**

* `apolo image ls`: Lists images in your current project and cluster. Supports filtering by name, organization, and project.
* `apolo image tags`: Lists all tags associated with an image.
* `apolo image rm`: Removes images from the platform registry.
* `apolo image size`: Shows the size of a specific image.
* `apolo image digest`: Retrieves the digest of an image from the remote registry.

### **Working with Images**

The most commonly used operations are pushing and pulling images. Here's how to use them effectively:

Pushing images to the registry:

```bash
# Push a local image using the 'latest' tag
apolo image push myimage

# Push with a specific tag and destination
apolo image push alpine:latest image:my-alpine:production

# Push to a different project
apolo image push alpine image:/other-project/alpine:shared
```

Pulling images from the registry:

```bash
# Pull an image to your local environment
apolo image pull image:myimage

# Pull with a different local name
apolo image pull image:/project/my-alpine:production alpine:from-registry

# Pull from another project
apolo image pull image:/other-project/alpine:shared
```

### **Image Naming Convention**

When working with the CLI, image names follow a specific URL scheme:

* Remote images must use the `image://` scheme
* Tags can be specified using the colon notation (e.g., `image:myapp:v1.0`)
* Cross-project references use the format `image:/other-project/imagename:tag`

Note: All commands provide additional help information through the `--help` option, which details available flags and usage examples.

### **Using External Images**

While the Images app and CLI provide tools for managing images in the platform registry, you can also run jobs using external Docker images. Simply specify the full image path when using `apolo job run`:

```bash
# Run a job using an image from Docker Hub
apolo job run pytorch/pytorch:latest

# Run a job with a custom entrypoint using an external image
apolo job run --preset=gpu-small --entrypoint=/custom-script.sh tensorflow/tensorflow:latest -- arg1 arg2
```

This flexibility allows you to leverage both platform-hosted images and publicly available container images from Docker Hub or other registries, depending on your needs. The same job configuration options apply regardless of whether you're using internal or external images.

### **Working with Images in Workflows**

Apolo Workflows provide a powerful way to automate image building and management as part of your development process. While you can build images directly using the CLI, workflows offer more sophisticated features for image handling, especially when working with complex build requirements or multiple image variants.

#### **Defining Images in Live Workflows**

In your `.apolo/live.yml` file, you can define images under the `images` section. Each image definition can specify its build context, Dockerfile location, build arguments, and resulting reference. Here's an example:

```yaml
images:
  training_image:
    context: ./training
    dockerfile: Dockerfile.training
    ref: image:training:${{ hash_files('training/Dockerfile.training', 'training/requirements.txt') }}
    build_args:
      - PYTHON_VERSION=3.9
    
  inference_image:
    context: ./inference
    ref: image:inference:latest
    build_preset: gpu-small
    env:
      CUDA_VERSION: "11.8"
```

This workflow defines two images with different configurations. The `training_image` uses content-based tagging through the `hash_files` function, which automatically generates a new tag when the source files change. The `inference_image` uses a GPU-enabled preset for its build process.

#### **Using Workflow Images in Jobs**

Once defined, these images can be referenced in your workflow jobs:

```yaml
jobs:
  train_model:
    image: ${{ images.training_image.ref }}
    preset: gpu-large
    volumes:
      - storage:data:/data
    cmd: python train.py --data-path /data

  serve_model:
    image: ${{ images.inference_image.ref }}
    http_port: 8080
    env:
      MODEL_PATH: /models/latest
```

#### **Building Images with Workflows**

To build images defined in your workflow, use the `apolo-flow build` command:

```bash
# Build a specific image
apolo-flow build training_image

# Force rebuild even if the image exists
apolo-flow build -F inference_image
```

The workflow system handles the entire build process, including:

* Setting up the build environment according to your specifications
* Managing build arguments and environment variables
* Uploading the build context to the platform
* Building the image using Kaniko in the cloud
* Publishing the resulting image to the platform registry

#### **Advanced Build Features**

Workflows provide several advanced features for image building:

* Content-based tagging using `hash_files()` for automatic version management
* Build-time secret management through environment variables and mounted volumes
* Resource preset selection for builds requiring specific hardware
* Build argument and environment variable interpolation using workflow expressions

This integration of image building into workflows allows you to create reproducible, automated processes for managing your container images as part of your larger application lifecycle.

For detailed information about workflow syntax and capabilities, refer to our [workflow documentation](https://docs.apolo.us/index/apolo-flow-reference). All image-related operations in workflows follow the same naming conventions described in the CLI section above.

### **Building Custom Images without Workflows**

The Apolo Extras CLI provides powerful tools for building and managing custom container images. This functionality is available through the `apolo-extras image` command suite, which offers two main approaches to building images:

Remote Building:

```bash
# Build an image remotely using Kaniko
apolo-extras image build ./app-context image:myapp:v1.0

# Build with custom Dockerfile and build arguments
apolo-extras image build -f custom.Dockerfile --build-arg VERSION=1.0 ./app-context image:myapp:latest
```

Local Building:

```bash
# Build an image locally using your Docker daemon
apolo-extras image local-build ./app-context image:myapp:v1.0
```

The remote build option uses Kaniko to construct images directly on the cluster, which is particularly useful when you don't have Docker installed locally or need to access cluster-specific resources during the build process. The local build option leverages your local Docker daemon, providing a familiar development experience.

Additionally, you can transfer images between clusters using:

```bash
apolo-extras image transfer image:myapp:v1.0 image:/other-cluster/myapp:v1.0
```

For detailed information about image building options, including advanced Kaniko configurations and build customization, please refer to the [Apolo Extras CLI](https://app.gitbook.com/s/EicNFI9vPOX1TTMYRKT9/) documentation.
