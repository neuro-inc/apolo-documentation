# Triton Model Repository

## Overview

The **Triton Model Repository** application registers a directory of Apolo Files that holds a Triton model repository. Once installed, other apps pick it as an integration, so the path is entered once.

The application is deployment-less: it does not run any workloads and becomes healthy immediately after installation. [Triton Inference Server](triton-inference-server.md) is the app that uses it. You can also give that app the path directly and skip this one.

## Prerequisites

A directory in Apolo Files, in the project where the apps will be installed, with one sub-directory per model. Each model directory holds a `config.pbtxt` and numbered version directories with the model files. The layout is described by NVIDIA: [Model Repository](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_repository.html#repository-layout).

## Installing

1. Open the **Apps** section of the **Apolo Console**
2. Find **Triton Model Repository** in the list of available apps
3. Click **Install**
4. Enter the **Repository Path**, the storage path of the directory, for example `storage:triton/models`
5. Click **Install**. The app becomes healthy right away

The path is checked when you install the app and when you change it. It is refused when:

* it is not in the project where you install the app
* it does not exist, or you have no access to it
* it is a file, not a directory

An existing empty directory is accepted.

## Using the Repository

In the **Triton Model Repository** field of [Triton Inference Server](triton-inference-server.md), pick the installed app through **App Integration**.

## Limits

* The Triton Inference Server app takes the path when you install or reconfigure it. If you later change the path in the Triton Model Repository app, reconfigure the server to use the new one
* An app that uses the repository must be uninstalled first. The platform refuses to uninstall the Triton Model Repository app while another app refers to it, unless you pass `--force`
* The directory must stay in Apolo Files of the same project. Files in it are not copied

## References

* [Triton Inference Server](triton-inference-server.md)
* [Model repository layout (NVIDIA)](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/user_guide/model_repository.html#repository-layout)
