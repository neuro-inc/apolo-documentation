# Service Deployment

## Overview

The **Service Deployment** application empowers Apolo users to deploy any containerized application within a Kubernetes environment. It offers seamless integration with various Apolo features, including public domain exposure with authentication, autoscaling, monitoring, logging and many more.

By utilizing the Service Deployment app, users can streamline their deployment processes, ensure consistent environments across development and production, and integrate seamlessly with Apolo's monitoring and logging tools for enhanced observability.

The Service Deployment app provides the following functionalities:

* **Container Deployment**: deploy any container image into the Kubernetes environment.
* **Public Domain Exposure**: optionally expose deployments under a public domain name with an optional platform authentication.
* **Autoscaling**: optionally, enable and configure Horizontal Pod Autoscaler (HPA) based on CPU and memory utilization. More exotic auto-scaling configuration will be available soon with integration of Keda autoscaler.
* **Monitoring and Logging**: Collect logs and monitor the deployment's performance.
* **Integrations support**: integrate with other solutions to support the deployment, like using custom private container registry.

#### Use Cases

* **Web Services**: Deploy RESTful APIs or web applications that require public access with secure authentication mechanisms.
* **Microservices Architecture**: Manage and scale microservices independently, leveraging Apolo's autoscaling features to handle varying loads efficiently.
* **Data Processing Pipelines**: Run data ingestion, transformation, and processing tasks that can benefit from containerization and orchestration.
* **Machine Learning Inference**: Serve machine learning models in production, ensuring high availability and scalability.
* **Custom Applications**: Deploy bespoke applications tailored to specific business needs, integrating with other services and tools within the Apolo ecosystem.

## Installing

The easiest way to learn is by doing, so in this case we are going to deploy simplistic web server, a hashicorp/http-echo container that has the only job — respond to the request with the configured payload.

{% stepper %}
{% step %}
**Select "Service Deployment" application at Apolo web console**

Access the Apolo web console and go to the "Apps" section. We presume you are already authorized in web console and a participant of organization and project.

<figure><img src="../../../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Service deployment in list of apps</p></figcaption></figure>

Now we configure application parameters
{% endstep %}

{% step %}
### Configure application

**Resource Preset:** Here you select an appropriate preset that specifies CPU, memory and GPU resources for your workload.&#x20;

For our use-case some tiny preset should work well, e.g., `cpu-mini`.

**Container Image**: Specify the container image repository and tag. Optionally, you could attach image pull secrets from dockerhub or other container image registry app.&#x20;

For our example, we set container image repository to `hashicorp/http-echo`, tag to `1.0.0` and leave registry secrets integration blank. We do not need custom registry integration int this case.

**Autoscaling:** Enable and configure HPA settings, including minimum and maximum replicas, and target CPU and memory utilization percentages.&#x20;

Here even though the API load will be tiny, we still enable it with 1 min and 3 max replicas and set autoscaling triggers at 70% for both CPU and memory consumption.&#x20;

**Container**: allows one to adjust container parameters. For instance, set any necessary arguments for the container command or the command itself. One can also add environment variables, either with plain text values, or mounted as platform secrets. Here you can also configure Apolo Files mounts to be accessible from within the deployed container.

In our case, we add two arguments for the server, `-text="hello apolo"` and `-listen=:8080` so it serves static text for HTTP request on the port 8080. As for the environment variables or storage mounts, we do not to add anything for now.

**Networking**: Configure which ports should be exposed, ingress settings, and port mappings.

In our example, we protect HTTP ingress with platform auth services and expose a single port `8080` matching the previous configuration and expose it with a prefix path type with path `/`.&#x20;

**Healthcheks:** help you to monitor liveness of the service and restart it if needed. This configuration is quite flexible, allowing one to monitor HTTP, gRPC endpoints, TCP socket or just simple bash command (exec). For HTTP and gRPC the response should be of 2xx status code, TCP connection should be opened and exec should exit with status code 0.

Enable "Health check probes" and set startup & readiness probes to perform`HTTP` health check with 30 seconds startup delay, 10 seconds period and allow it up to 5 failures before triggering the restart of the container. Set healthcheck timeout to 5 seconds.
{% endstep %}

{% step %}
### Review configuration

Now we can review the configuration.

{% tabs %}
{% tab title="Resource preset & container image" %}
<figure><img src="../../../../.gitbook/assets/image (1) (2).png" alt=""><figcaption><p>Resources and image</p></figcaption></figure>
{% endtab %}

{% tab title="Autoscaling" %}
<figure><img src="../../../../.gitbook/assets/image (2) (2).png" alt=""><figcaption><p>Autoscaling configuration</p></figcaption></figure>
{% endtab %}

{% tab title="Container" %}
<figure><img src="../../../../.gitbook/assets/image (7) (2).png" alt=""><figcaption><p>Container configuration</p></figcaption></figure>
{% endtab %}

{% tab title="Networking" %}
<figure><img src="../../../../.gitbook/assets/image (8) (2).png" alt=""><figcaption><p>Networking 1</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (9).png" alt=""><figcaption><p>Networking 2</p></figcaption></figure>
{% endtab %}

{% tab title="Healthchecks" %}
<figure><img src="../../../../.gitbook/assets/image (10).png" alt=""><figcaption><p>Startup probes 1</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (15).png" alt=""><figcaption><p>Startup probes 2</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (13).png" alt=""><figcaption><p>Readiness probe 1</p></figcaption></figure>

<figure><img src="../../../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Readiness probe 2</p></figcaption></figure>
{% endtab %}
{% endtabs %}
{% endstep %}

{% step %}
### Triggering installation

After clicking the "install" button, you will be redirected to the application details page and Apolo will take care of application installation.

<figure><img src="../../../../.gitbook/assets/image (18).png" alt=""><figcaption><p>Start application installation</p></figcaption></figure>

In a minute, you could hit "refresh" button or wait while the web console updates status of the application. It should switch into "healthy" state. Now you could use the application.
{% endstep %}
{% endstepper %}

As for the approach of managing service deployment applications via CLI, see dedicated the instructions on a [documentation dedicated page](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/service-deployment.md).

## Usage

The application usage highly depend on what is deployed under the hood. By default, the application outputs include two HTTP endpoints, if at least one HTTP port was configured, as in our example.

To view these endpoint details, you could scroll down application details page.

<figure><img src="../../../../.gitbook/assets/image (20).png" alt=""><figcaption><p>Application outputs</p></figcaption></figure>

### Access via public domain

In our example, the publicly available domain name is displayed in the list of application outputs. It is also protected by platform auth, and in-cluster domain name are displayed. In order to use public domain name, one should have access to the same organization, cluster and project. Let's verify it.

{% tabs %}
{% tab title="Authorized access (the same user)" %}
<figure><img src="../../../../.gitbook/assets/image (25).png" alt=""><figcaption><p>HTTP Echo application response</p></figcaption></figure>
{% endtab %}

{% tab title="Unauthorized user" %}
<figure><img src="../../../../.gitbook/assets/image (23).png" alt=""><figcaption><p>Explicit error stating the user do not have access</p></figcaption></figure>
{% endtab %}

{% tab title="Incognito mode" %}
<figure><img src="../../../../.gitbook/assets/image (24).png" alt=""><figcaption><p>User is redirected to login first</p></figcaption></figure>
{% endtab %}
{% endtabs %}

### Intra-cluster access

In many cases, you'll need to access your applications from other applications or from within the jobs. For this case, each application's public domain endpoint has a corresponding intra-cluster sibling with some differences though:

1. Intra-cluster endpoints obviously are not accessible outside of the cluster or other clusters
2. Intra-cluster endpoints are not protected by platform auth, neither they have TLS (at least, for now).
3. Intra-cluster endpoints are not limited to HTTP-only traffic, you could also communicate in binary protocols on the TCP/UDP levels.
4. Intra-cluster endpoints are only accessible from within the workloads that belong to the same project.
5. Intra-cluster endpoints are not "port-forwarded" to 443 port, instead, you should connect to the same port you specified in the configuration file.

For our case, let's connect to the application from within the simplistic `curl` job & hit the **HTTP API** endpoint with a GET request:

<pre><code>$ apolo run curlimages/curl -- <a data-footnote-ref href="#user-content-fn-1">apolo-default-service-deployment-39ba8a4d-custom-deployment.apolo-default-service-deployment-39ba8a4d</a>:<a data-footnote-ref href="#user-content-fn-2">8080</a> -v 
√ Job ID: job-23832f68-e890-4412-b46b-f3679e8f9e41
- Status: pending Creating
- Status: pending Scheduling
- Status: pending Completed
√ Status: succeeded
√ =========== Job is running in terminal mode ===========
√ (If you don't see a command prompt, try pressing enter)
√ (Use Ctrl-P Ctrl-Q key sequence to detach from the job)
* Host apolo-default-service-deployment-39ba8a4d-custom-deployment.apolo-default-service-deployment-39ba8a4d:8080 was resolved.
* IPv6: (none)
* IPv4: 10.107.101.244
*   Trying 10.107.101.244:8080...
* Connected to apolo-default-service-deployment-39ba8a4d-custom-deployment.apolo-default-service-deployment-39ba8a4d (10.107.101.244) port 8080
* using HTTP/1.x
> GET / HTTP/1.1
> Host: apolo-default-service-deployment-39ba8a4d-custom-deployment.apolo-default-service-deployment-39ba8a4d:8080
> User-Agent: curl/8.13.0
> Accept: */*
> 
* Request completely sent off
&#x3C; HTTP/1.1 200 OK
&#x3C; X-App-Name: http-echo
&#x3C; X-App-Version: 1.0.0
&#x3C; Date: Fri, 30 May 2025 14:31:05 GMT
&#x3C; Content-Length: 14
&#x3C; Content-Type: text/plain; charset=utf-8
&#x3C; 
"hello apolo"
* Connection #0 to host apolo-default-service-deployment-39ba8a4d-custom-deployment.apolo-default-service-deployment-39ba8a4d left intact
</code></pre>

## Cleanup

When the application is not needed anymore, you could remove it by clicking the "uninstall" button on the installed app details/status screen.

## References

* [Apolo CLI Application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
* [Apolo CLI Application template commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo CLI Service deployment application management](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/service-deployment.md)
* [Docker hub hashicorp/http-echo image](https://hub.docker.com/r/hashicorp/http-echo)
* [Docker hub curlimages/curl image](https://hub.docker.com/r/curlimages/curl)

[^1]: HTTP API domain name

[^2]: HTTP API port
