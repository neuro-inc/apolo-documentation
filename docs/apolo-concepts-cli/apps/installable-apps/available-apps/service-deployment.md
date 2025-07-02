# Service Deployment

A detailed description of this application at the dedicated Apolo Console's [service-deployment.md](../../../../apolo-console/apps/installable-apps/available-apps/service-deployment.md "mention") page.

## Managing application via Apolo CLI

The prerequisite for this tutorial is that you read the description of the application via the link mentioned above

{% stepper %}
{% step %}
### Load application installation template

```
apolo app-template get service-deployment > service-deployment.yaml
```
{% endstep %}

{% step %}
### **Fill in required parameters**

We configure the application identically to the configuration discussed in the web console installation case.

In a nutshell, we deploy `hashicorp/http-echo:1.0.0` with auto scaling, exposting port 8080 and protecting it with authentication. We also enabled startup & readiness healthcheck probes.&#x20;

```yaml
template_name: service-deployment
display_name: myhttpecho
input:
  preset:
    name: cpu-mini
  image:
    tag: 1.0.0
    repository: hashicorp/http-echo
    dockerconfigjson: null
  autoscaling:
    max_replicas: 3
    min_replicas: 1
    target_cpu_utilization_percentage: 70
    target_memory_utilization_percentage: 70
  container:
    env: []
    args:
      - '-text="hello apolo"'
      - '-listen=:8080'
    command: null
  storage_mounts: null
  networking:
    ports:
      - name: http
        path: /
        port: 8080
        path_type: Prefix
    ingress_http:
      auth: true
    service_enabled: true
  health_checks:
    startup:
      period: 10
      timeout: 5
      initial_delay: 30
      failure_threshold: 5
      health_check_config:
        path: /
        port: 8080
        probe_type: HTTP
        http_headers: null
    liveness: null
    readiness:
      period: 10
      timeout: 5
      initial_delay: 30
      failure_threshold: 5
      health_check_config:
        path: /
        port: 8080
        probe_type: HTTP
        http_headers: null
```

You could also configure specific version of the application to be installed by adding `template_version: <version>` block to the config file. If it is not there, the `latest` version is assumed.
{% endstep %}

{% step %}
### Install the application

```
$ apolo app install -f service-deployment.yaml 
App installed from service-deployment.yaml
```

The application status could be also displayed via CLI:

```
$ apolo app ls                                                    
                                                                                                                                                
  ID                                     Name                                        Display Name   Template             Version   State        
 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 
  39ba8a4d-cff8-4f8c-b536-253f176d182d   apolo-default-service-deployment-39ba8a4d   myhttpecho     service-deployment   v25.5.0   progressing  

```
{% endstep %}

{% step %}
### Removal

If you want to remove the application via CLI, use `apolo app remove <app-id>`.
{% endstep %}
{% endstepper %}

You can find more information for application management commands in the list of references below.

## References

* [Apolo Service Deployment application](../../../../apolo-console/apps/installable-apps/available-apps/service-deployment.md)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
