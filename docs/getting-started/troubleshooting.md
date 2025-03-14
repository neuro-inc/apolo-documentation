# Troubleshooting

## Lost/unknown job ID

The first step in any investigation is knowing a job ID. If you started your job with `apolo run`, the job's ID was printed in the output.

However, if you can't find the initial terminal output, you can use one of these commands to find a specific job:

{% tabs %}
{% tab title="apolo CLI" %}
Note: `apolo ps` is a shortcut for `apolo job ls`

`apolo ps` prints only running jobs.\
`apolo ps -a` prints all jobs.\
`apolo ps -s failed` prints all jobs with the Failed status.
{% endtab %}

{% tab title="apolo-flow" %}
Run `apolo-flow ps` to get the list of all jobs defined in a flow.
{% endtab %}
{% endtabs %}

## Image build failed

When you run `apolo-flow build IMAGE_NAME`, apolo-flow uploads the build context to the platform and creates a platform job that uses [Kaniko](https://github.com/GoogleContainerTools/kaniko) to build a docker image and push it to the platform registry.

If building fails, you can check the job's status and logs to get more information.

#### Getting a job's status

To check a job's status, run:

```
apolo status <job-ID> 
```

The **Status transitions** section in the output can help you learn at which step the job failed.

#### Getting builder logs

To check builder logs, run:

```
apolo logs <job-ID> 
```

## apolo run / apolo-flow run failed

There are a few main reasons your job may fail. Here are some of the most common:

#### Incorrect image name

This can happen if you have a typo in the image name or if the specified image was not built before running a job. List of all images can be accessed by running `apolo image ls`. You can also list tags for a particular image via `apolo image tags <IMAGE_URI>`.

#### Incorrect volume mounted

You might have an invalid volume mounted to the job. For example, you've mounted a volume to the `/my-project` folder, but your code expects `/my_project`. You can double-check it in the logs.

#### Cluster scale up failed

If you see a **Cluster Scale Up Failed** error in the status, it usually means you’ve requested resources that are not available in the cluster at the moment. For example, all GPUs are busy, so your job can’t be scheduled.

#### Code issues

You may have an error in your python script that prevents the job from running properly.

## Can’t access my job via HTTP

There are a few steps to troubleshooting such issues.

#### Checking for an open HTTP port

The first point of interest is whether you have an open HTTP port for your job. To check this, you can:

{% tabs %}
{% tab title="apolo CLI" %}
Use the `--http_port` parameter.
{% endtab %}

{% tab title="apolo-flow" %}
Use the `http_port:` option.
{% endtab %}
{% endtabs %}

#### Checking the listening IP

Next, make sure that your web app listens on 0.0.0.0, not on 127.0.0.1 or `localhost` — otherwise it won't be able to accept incoming requests from the outside of the container.

#### Disabling HTTP authentication

And finally, if you can access your job via browser, but `curl` and similar tools don’t work, most likely you didn’t disable HTTP authentication. The Apolo platform puts an HTTP authentication layer in front of your app by default for security reasons.

You can disable this behavior manually when running jobs:

{% tabs %}
{% tab title="apolo CLI" %}
Use the `--no-http-auth` parameter.
{% endtab %}

{% tab title="apolo-flow" %}
Use the `http_auth: False` option.
{% endtab %}
{% endtabs %}

## Troubleshooting a running job

Just like with Docker, you can get a shell in a running job to check its state. To do this, run:

```
apolo exec JOB_ID -- /bin/sh
```

Note: In Docker you would typically add the `-it` parameters to the command, but they’re not necessary for `apolo exec`.

## Navigating job statuses

A job might have one of the following statuses:

| Status        | Description                                                                                                                                                     |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **pending**   | The job is being created and scheduled. This includes finding (and possibly waiting for) sufficient amount of resources, pulling an image from a registry, etc. |
| **suspended** | The job's execution was temporarily suspended                                                                                                                   |
| **running**   | The job is running                                                                                                                                              |
| **succeeded** | The job terminated with the exit code _0_                                                                                                                       |
| **cancelled** | The running job was manually terminated/deleted                                                                                                                 |
| **failed**    | The job terminated with a non-_0_ exit code.                                                                                                                    |

Each of these statuses have additional sub-statuses that can help you monitor the job and trace errors on an more granular level:

| Sub-status           | Description                                                               |
| -------------------- | ------------------------------------------------------------------------- |
| PodInitializing      | Initializing a pod for the job                                            |
| ContainerCreating    | Creating a container for the job                                          |
| ErrImagePull         | Failed to pull the specified image                                        |
| ImagePullBackOff     | Stopping image pulling                                                    |
| InvalidImageName     | Incorrect image name was specified                                        |
| OOMKilled            | Job terminated due to an Out Of Memory error                              |
| Completed            | Job is completed                                                          |
| Error                | An error occured                                                          |
| ContainerCannotRun   | Cannot run the container                                                  |
| Creating             | Creating the job                                                          |
| Collected            | Job collected                                                             |
| Scheduling           | Scheduling the job execution                                              |
| NotFound             | The job could not be scheduled or was preempted                           |
| ClusterNotFound      | Specified cluster was not found                                           |
| ClusterScalingUp     | Scaling up                                                                |
| ClusterScaleUpFailed | Failed to scale up the cluster                                            |
| Restarting           | Restarting the job                                                        |
| DiskUnavailable      | Specified disk is currently unavailable                                   |
| QuotaExhausted       | User quota was exhausted - you will need to renew it to perform more jobs |
| LifeSpanEnded        | The job has reached the end of its lifespan                               |
| UserRequested        | The job was terminated per user request                                   |

##
