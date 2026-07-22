# Getting Started

## Introduction

There are two things you will need to do before you start working with Apolo:

1. [Install the CLI client](../../apolo-concepts-cli/installing.md).
2. [Understand the platform's main concepts](getting-started.md#understanding-the-main-concepts).

After this, you're free to explore the platform and it's functionality. As a good starting point, we've included a section about [development on GPU with Jupyter Notebooks](getting-started.md#developing-on-gpu-with-jupyter-notebooks).

## Understanding the main concepts

On the **Apolo** level, you will work with jobs, environments, and storage. To be more specific, a job (an execution unit) runs in a given environment (Docker container) on a given preset (a combination of CPU, GPU, and memory resources allocated for this job) with several storage instances (block or object storage) attached.

Here are some examples.

### Hello, World!

Run a job on CPU which prints “Hello, World!” and shuts down:

```bash
apolo run --preset cpu-small --name test ubuntu -- echo Hello, World!
```

Executing this command will result in an output like this:

```
√ Job ID: job-7dd12c3c-ae8d-4492-bdb9-99509fda4f8c
√ Name: test
- Status: pending Creating
- Status: pending Scheduling
- Status: pending ContainerCreating
√ Http URL: https://test--jane-doe.jobs.default.org.apolo.us
√ The job will die in a day. See --life-span option documentation for details.
√ Status: succeeded
√ =========== Job is running in terminal mode ===========
√ (If you don't see a command prompt, try pressing enter)
√ (Use Ctrl-P Ctrl-Q key sequence to detach from the job)
Hello, World!
```

### A simple GPU job

Resource presets are cluster-specific, so first check which GPU presets are available on your cluster:

```
apolo config show
```

Run a job on GPU in the default Apolo environment (`ghcr.io/neuro-inc/base`) that checks if CUDA is available in this environment. In this image, ML frameworks live in dedicated conda environments (`torch` for PyTorch, `tf` for TensorFlow), so the command must be executed in the corresponding environment via `conda run`:

```
apolo run --preset <gpu-preset-name> --name test ghcr.io/neuro-inc/base -- conda run -n torch python -c "import torch; print(torch.cuda.is_available());"
```

Replace `<gpu-preset-name>` with one of the GPU presets listed by `apolo config show` (for example, `gpu-l4-x1` or `gpu-a100-x1`).

### Working with platform storage

Create a new `demo` directory in the root directory of your platform storage:

```
apolo mkdir -p storage:demo
```

Run a job that mounts the `demo` directory from platform storage to the `/demo` directory in the job container and creates a file in it:

```
apolo run --volume storage:demo:/demo:rw ubuntu -- bash -c "echo Hello >> /demo/hello.txt"
```

Check that the file you have just created is actually on the storage:

```
apolo ls storage:demo
```

## Developing on GPU with Jupyter Notebooks

Development in Jupyter Notebooks is a good example of how the Apolo Platform can be used. While you can run a Jupyter Notebooks session in one command through CLI or in one click in the Console, we recommend project-based development. To simplify the process, we provide a project template which is based on the [**cookiecutter** package](https://github.com/cookiecutter/cookiecutter). This template provides the basic necessary folder structure and integrations with several recommended tools.

### Initializing a Apolo cookiecutter flow

First, you will need to install the **cookiecutter** package via **pip** or **pipx**:

```
pipx install cookiecutter
```

Now, to initialize a new Apolo flow using [cookiecutter](https://github.com/neuro-inc/cookiecutter-neuro-project/blob/master/cookiecutter.json) template, run:

```
cookiecutter gh:neuro-inc/cookiecutter-neuro-project --checkout release
```

This command will prompt you to enter some info about your new flow:

```
[1/6] flow_name (My flow): My Cookiecutter Flow
[2/6] flow_description ():
[3/6] flow_dir (my cookiecutter flow):
[4/6] flow_id (my_cookiecutter_flow):
[5/6] code_directory (modules):
[6/6] preserve Apolo Flow template hints (yes):
```

{% hint style="info" %}
Default values are indicated by round brackets **( )**. You can use them by pressing **Enter**. Note that the default `flow_dir` is derived from `flow_name` and may contain spaces — consider entering a directory name without spaces to simplify shell commands.
{% endhint %}

To navigate to the flow directory, run:

```
cd my-cookiecutter-flow
```

### Flow structure

The structure of the project's folder will look like this:

```
my-cookiecutter-flow
├── .github/            <- Github workflows and a dependabot.yml file
├── .apolo/             <- apolo and apolo-flow CLI configuration files (live.yml, project.yml)
├── config/             <- configuration files for various integrations
├── data/               <- training and testing datasets (we don't keep it under source control)
├── notebooks/          <- Jupyter notebooks
├── modules/            <- models' source code
├── results/            <- training artifacts
├── .gitignore          <- default .gitignore file for a Python ML project
├── .apolo.toml         <- autogenerated config file for Apolo CLI
├── .apoloignore        <- a file telling Apolo CLI which files to ignore while uploading to the platform storage
├── HELP.md             <- autogenerated template reference
├── README.md           <- autogenerated informational file
├── Dockerfile          <- description of the docker image used for training in your flow
├── apt.txt             <- list of system packages to be installed in the training environment
├── requirements.txt    <- list of Python dependencies to be installed in the training environment
├── setup.cfg           <- linter settings (Python code quality checking)
└── update_actions.py   <- script used to update apolo-flow actions in one of the GitHub workflows
```

The template contains the `.apolo/live.yml` configuration file for `apolo-flow`. This file guarantees a proper connection between the flow structure, the base environment that we provide, and actions with storage and jobs. For example, the `upload` command synchronizes sub-folders on your local machine with sub-folders on the persistent platform storage, and those sub-folders are synchronized with the corresponding sub-folders in job containers.

### Setting up the environment and running Jupyter

To set up the project environment, run:

```
apolo-flow build train
apolo-flow mkvolumes
```

When these commands are executed, system packages from `apt.txt` and pip dependencies from `requirements.txt` are installed to the base environment. It supports CUDA by default and contains the most popular ML/AI frameworks such as Tensorflow and Pytorch (available in the dedicated `tf` and `torch` conda environments respectively).

For Jupyter Notebooks to run properly, the `train.py` script and the notebook itself should be available on the storage. Upload the `code` directory containing this file to the storage by using the following command:

```
apolo-flow upload ALL
```

Now you need to choose a preset on which you want to run your Jupyter jobs. To view the list of presets available on the current cluster, run:

```
apolo config show 
```

The `release` template does not include a `jupyter` job out of the box (it defines the `train`, `multitrain`, and `remote_debug` jobs). To add one, extend the `jobs:` section of `.apolo/live.yml` with the [apolo-actions/jupyter](https://github.com/apolo-actions/jupyter) action:

```yaml
  jupyter:
    action: gh:apolo-actions/jupyter@v24.11.0
    args:
      image: $[[ images.train.ref ]]
      preset: <preset-name>
      jupyter_mode: lab
      multi_args: $[[ multi.args ]]
      volumes_data_remote: $[[ volumes.data.remote ]]
      volumes_code_remote: $[[ volumes.code.remote ]]
      volumes_config_remote: $[[ volumes.config.remote ]]
      volumes_notebooks_remote: $[[ volumes.notebooks.remote ]]
      volumes_results_remote: $[[ volumes.results.remote ]]
```

Replace `<preset-name>` with the preset you chose in the previous step. Now, to start a Jupyter session, run:

```
apolo-flow run jupyter
```

This command will open the Jupyter interface in your default browser.

{% hint style="info" %}
[You can find more information about the jupyter action's arguments here](https://github.com/apolo-actions/jupyter#arguments)
{% endhint %}

Now, when you edit notebooks, they are updated on your platform storage. To download them locally (for example, to save them under a version control system), run:

```
apolo-flow download notebooks
```

Don’t forget to terminate your job when you no longer need it (the files won’t disappear after that):

```
apolo-flow kill jupyter
```

To check how many credits you have left, run:

```
apolo config show
```
