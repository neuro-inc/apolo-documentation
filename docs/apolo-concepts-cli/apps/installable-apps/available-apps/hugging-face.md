# Hugging Face

See more detailed description of the application at the dedicated Apolo Console's [hugging-face.md](../../../../apolo-console/apps/installable-apps/available-apps/hugging-face.md "mention") page.

## Managing application via Apolo CLI

{% stepper %}
{% step %}
### Load application installation template

```
apolo app-template get hugging-face-cache > hf-cache.yaml
```
{% endstep %}

{% step %}
### **Fill in required parameters**

```yaml
template_name: hugging-face-cache
input:
  cache_config:
    files_path:
      path: storage://<cluster>/<org>/<project>/your/sub/path
```

You could also configure specific version of the application to be installed by adding `template_version: <version>` block to the config file. If it is not there, the `latest` version is assumed.
{% endstep %}

{% step %}
### Install the application

`apolo app install -f hf-cache.yaml`&#x20;

The application status could be also displayed via CLI:

```
apolo app ls

  ID                                     Name                                                          Display Name                      Template                          Version   State        
 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 
  86c60e06-ac1f-442c-a20f-836a2533cf1a   apolo-apoloproject-hugging-face-cache-86c60e06                Hugging Face Cache                hugging-face-cache                HEAD      healthy      

```
{% endstep %}

{% step %}
### Removal

If you want to remove the application via CLI, use `apolo app remove <app-id>`.
{% endstep %}
{% endstepper %}

You can find more information for application management commands in the list of references below.

## References

* [Apolo Hugging Face application](../../../../apolo-console/apps/installable-apps/available-apps/hugging-face.md)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
* [Hugging Face Hub Caching Guide](https://huggingface.co/docs/huggingface_hub/en/guides/manage-cache)
* [Apolo LLM Inference application](../../../../apolo-console/apps/installable-apps/available-apps/llm-inference/)
* [Apolo Text Embeddings application](../../../../apolo-console/apps/installable-apps/available-apps/text-embeddings-inference.md)
