# Hugging Face

See more detailed description of the application at the dedicated Apolo Console's [hugging-face.md](../../../apolo-console/apps/available-apps/hugging-face.md "mention") page.

## Managing application via Apolo CLI

### Installation

**Step 1** — load application installation template

```
apolo app-template get hugging-face-cache -o hf-cache.yaml
```

**Step 2** — fill in required parameters

```yaml
template_name: hugging-face-cache
template_version: latest
input:
  cache_config:
    files_path:
      path: storage://<cluster>/<org>/<project>/your/sub/path
```

**Step 3** — install the application

`apolo app install -f hf-cache.yaml`&#x20;

The application status could be also displayed via CLI:

```
apolo app ls

  ID                                     Name                                                          Display Name                      Template                          Version   State        
 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 
  86c60e06-ac1f-442c-a20f-836a2533cf1a   apolo-apoloproject-hugging-face-cache-86c60e06                Hugging Face Cache                hugging-face-cache                HEAD      healthy      

```

**Step 4** — if you want to remove the application via CLI, use `apolo app remove <app-id>`.

You can find more information for application management commands in the list of references below.

## References

* [Apolo Hugging Face application](../../../apolo-console/apps/available-apps/hugging-face.md)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
