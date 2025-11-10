# FAQ

### How to Upload and Download Data

You can upload your datasets to the Platform using Neuro CLI. Neuro CLI supports basic file system operations for copying and moving files to and from the platform storage.

From your terminal or command prompt, change to the directory containing your dataset, and run:

```
neuro cp -r data/ storage:data/
```

The URI `storage:data/` indicates that the destination is the platform. In a similar fashion,

```
neuro cp -r storage:data/ data/
```

downloads dataset to your current directory locally.

You can access your dataset from within a container by giving `--volume storage:data/:/var/storage/data/:rw` to `neuro run` as a parameter when starting a new job.

If your aim is to download or upload data from external system, be it AWS S3 bucket, or Azure Blob Storage, checkout our [extras](broken-reference) package usage.

### How to Connect to a Running Job

To work with your dataset from within a container, to troubleshoot a model, or to get shell access to a GPU instance, you can execute a command shell within a running job in interactive mode.

To do so, copy the job id of a running job (you can run `neuro ps` to see the list), and run:

```
neuro exec <job-id or job-name> bash
```

For example,

```
neuro exec training bash
```

This command starts bash within the running job and connects your terminal to it.

### How to Run a Job in a Custom Environment

Assuming you have a local Docker image named `helloworld` built on your local machine, you can push it to the Neuro Platform by running:

```
neuro push helloworld
```

After that, you can start the job by running:

```
neuro run image:helloworld
```

### How to Kill All Running Jobs

To kill all jobs that are currently running on your behalf, run the following command:

```
 neuro kill `neuro -q ps -o <user>`
```

For example,

```
 neuro kill `neuro -q ps -o mariyadavydova`
```

### How to Run Two or More Commands In a Job

Sometimes you want to execute two or three commands in a job without having to connect to it. For example, you may want to change the working directory and to run training. To achieve this, you need to wrap your commands in `”bash -c ‘<commands>’”` call, like this:

```
"bash -c 'cd /project && python mnist/train.py'"
```

### How to Get Output from a Running Job

There are two ways to get the output of your running job:

* Run it without the `--detach` option.
* Connect to a running job output with `neuro log <JOB>`, where JOB is either the id or the name of your job.

In some cases, Python caches the output of the scripts, so that you won’t get any output until the job finishes. To overcome this problem, provide `-u` option to `python`, like this:

```
"bash -c 'cd /project && python -u mnist/train.py'"
```
