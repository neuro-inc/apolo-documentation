# Service Deployment

## Overview

The **Service Deployment** application empowers Apolo users to deploy any containerized application within a Kubernetes environment. It offers seamless integration with various Apolo features, including public domain exposure with authentication, autoscaling, monitoring, logging and many more.

By utilizing the Service Deployment app, users can streamline their deployment processes, ensure consistent environments across development and production, and integrate seamlessly with Apolo's monitoring and logging tools for enhanced observability. To learn more about Service Deployment, refer to the main [Service Deployment](../../../../apolo-console/apps/installable-apps/available-apps/service-deployment.md) page for Apolo Console.

## Installing

### Installing via Apolo CLI

As for the approach of managing Service Deployment applications via Apolo Console, see dedicated the instructions on a [documentation dedicated page](../../../../apolo-console/apps/installable-apps/available-apps/service-deployment.md).

## Usage

**Step 1** — Obtain the application configuration file template:

```bash
apolo app-template get service-deployment > myservice.yaml
```

**Step 2** — Customize the application parameters. With Service Deployment, you can deploy long running scalable applications using container images and configure attributes like exposed ports, volume mounts, Apolo authentication and health checks.\
\
Below is an example configuration file that deploys a nginx server.

```yaml
# Example of myservice.yaml

template_name: service-deployment
template_version: v25.5.1
input:
  preset:
    name: 'cpu-small'
  image:
    repository: 'nginx'
    tag: latest
  networking:
    service_enabled: true
    ingress_http:
      auth: True
    ports:
      - name: http
        port: 80
        path_type: Prefix
        path: /
  health_checks:
    readiness:
      initial_delay: 10
      period: 5
      timeout: 2
      health_check_config:
        port: 80
        probe_type: HTTP
        path: /
  # autoscaling:
  #   max_replicas: 5
  #   min_replicas: 1
  #   target_cpu_utilization: 70
  #   target_memory_utilization: 80
  # container:
  #   command: []
  #   args: []
  #   env:
  #     - name: 'MY_ENV_VAR'
  #       value: 'my_value'
  # storage_mounts:
  #   - storage_uri: storage:directory
  #     mount_path: '/data'
  #     mode:
  #       mode: 'r'
```

**Step 3** — Deploy the application in your Apolo project:

```bash
apolo app install -f myservice.yaml
```

Monitor the application status using:

```bash
apolo app list
```

To uninstall the application, use:

```bash
apolo app uninstall <app-id>
```

If you want to see logs of the application, use:

```bash
apolo app logs <app-id>
```

For instructions on how to access the application, please refer to the [Usage](service-deployment-1.md#usage) section.

## Cleanup

When the application is not needed anymore, you could remove it by clicking the "uninstall" button on the installed app details/status screen.

## References

* [Apolo CLI Application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
* [Apolo CLI Application template commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo CLI Service deployment application management](service-deployment.md)
