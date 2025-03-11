# Remote Debugging with VS Code

### Introduction

In this tutorial, we will show how to set up remote debugging with VS Code on the platform using the flow template.

{% hint style="warning" %}
Remote debugging relies on a running SSH server in a job's 22 port. We ensure it for you if you use our base image (`ghcr.io/neuro-inc/base`).
{% endhint %}

### Initializing a new project

Make sure you have CLI and [**cookiecutter**](https://github.com/cookiecutter/cookiecutter) installed, refer to [getting-started.md](../../../../../../apolo-console/first-steps/getting-started.md "mention") for instructions.

Then, initialize an empty flow:

```bash
$ cookiecutter gh:neuro-inc/cookiecutter-neuro-project --checkout release
```

The project initialization command asks several questions about your project:

```
project_name [Name of the project]: Apolo VSCode
project_dir [apolo-vscode]:
project_id [apolo-vscode]:
code_directory [modules]: 
preserve Neuro Flow template hints [yes]:
```

### Configuring the project

Add `debugpy` to your project's `requirements.txt` file (located in the project's root folder):

```bash
apolo-flow

flake8
mypy
isort
black
debugpy
```

Add the following lines to the beginning of your code file. In this example, it's `modules/train.py`.

```bash
import debugpy
debugpy.listen(("0.0.0.0", 5678))
debugpy.wait_for_client()
```

Next, configure the project's environment on the platform:

```bash
$ apolo-flow build myimage
```

When the image is built, you can upload your code from a local file to the platform storage:

```
$ apolo-flow upload code
```

By default, this will upload everything from your project's `modules` folder to the `storage:<your_project_id>/modules` storage folder. To configure the source and the target for this command, go to your project's `.neuro/live.yml` file and find the `code` section under `volumes`:

```
volumes:
  data:
    remote: storage:$[[ flow.project_id ]]/data
    mount: /project/data
    local: data
  code:
    remote: storage:$[[ flow.project_id ]]/modules
    mount: /project/modules
    local: modules
  config:
    remote: storage:$[[ flow.project_id ]]/config
    mount: /project/config
    local: config
    read_only: True
```

Here, you can specify your local code folder and the storage folder you want to upload this code to.

### Running your code

In this example, we will be running a training job based on the code contained in the `train.py` file we just uploaded to the platform storage. To do this, run:

```
$ apolo-flow run train
```

Once the job is running, detach from it by pressing **Ctrl+P, Ctrl+Q** and run the following command:

```
$ apolo job port-forward <job-id> 5678:5678
```

This will allow you to access the job by the `5678` port.

### Debugging

Open your code file in VS Code and navigate to **Run > Start Debugging** or press **F5**:

![](</docs/.gitbook/assets/image (89) (1).png>)

Select **Remote Attach**:

![](</docs/.gitbook/assets/image (88).png>)

Enter **localhost** as the host name and the job's port number (in this case, it's **5687**):

![](</docs/.gitbook/assets/image (87) (1).png>)

![](</docs/.gitbook/assets/image (91).png>)

When this is done, you can set the breakpoint and start debugging.
