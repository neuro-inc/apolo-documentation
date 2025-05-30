# Hugging Face

The Hugging Face App on Apolo enables efficient management of Hugging Face model and dataset caches within Apolo's infrastructure. This integration ensures that large binary files are preserved in Apolo storage, facilitating seamless access and management. Future enhancements will include linking Hugging Face Hub user access tokens to bind accounts with caches, allowing authorized access to private models and datasets, as well as providing in-depth introspection of models, datasets, suggestions for inference and much more.

## Overview

The Hugging Face App provides the following functionalities:

* Manage cached models and datasets within Apolo's storage.
* Connect caches to inference models to preserve large binary files.
* (Upcoming) Link Hugging Face Hub user access tokens for authorized access.
* (Upcoming) Repositories introspection.

## Deployment on Apolo

#### Web Console UI

1. **Navigate to the Apolo Console**: Access the Apolo Console and go to the "Apps" section.
2. **Select "Hugging Face Cache" App**: Choose the "Hugging Face Cache" application from the list of available apps.

<figure><img src="../../../.gitbook/assets/image (26).png" alt=""><figcaption><p>Hugging Face Cache application</p></figcaption></figure>

3. **Configure storage**:

* **Files Path**: Select or type path on Apolo storage where assets will be preserved.

4. **Set application name Variables.**

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

4. **Click "install".**

Now you can use this cache to speedup load times of datasets or models from Hugging Face Hub.

## Usage

You could see the list of supportable applications in the description of this app (related applications).

The use of this app is mostly transparent for the user.

## References

* [Hugging Face Cache Management](https://huggingface.co/docs/datasets/en/cache)
* [Hugging Face Hub Caching Guide](https://huggingface.co/docs/huggingface_hub/en/guides/manage-cache)
* [Apolo LLM Inference Documentation](llm-inference/)
* [Apolo Hugging Face management via CLI](../../../apolo-concepts-cli/apps/available-apps/hugging-face.md)
