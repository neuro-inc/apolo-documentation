# Installing CLI

[Apolo Console](https://console.apolo.us/apps/shell/install) application doesn't require installation and can quickly get you familiar with Apolo, allowing you to work with the platform in a browser.

Installing Apolo CLI locally can be more efficient for long-term use, as your source code and other local files will be stored directly on your machine. Additionally, using Apolo CLI provides you with more flexible and extensive functionality, allowing for greater control and customization of your development environment.

## Installation instructions

{% tabs %}
{% tab title="Linux and Mac OS" %}
**Installing via pipx**

Our _apolo-all_ package available in pipx will automatically install all required components:

```
$ pip install pipx
$ pipx install apolo-all
$ pipx upgrade apolo-all
```

**Installing via pip**

You can also install all of the components through pip.

Apolo CLI requires Python 3 installed (recommended: 3.8; required: 3.7.9 or newer). We suggest using the [Anaconda Python 3.8 Distribution](https://www.anaconda.com/distribution/).

```
$ pip install -U apolo-cli apolo-extras apolo-flow
$ apolo login
```

If your machine doesn't have GUI, use the following command instead of apolo login:

```
$ apolo config login-headless
```
{% endtab %}

{% tab title="Windows" %}
**Installing via pipx**

Our _apolo-all_ package available in pipx will automatically install all required components:

```
$ pip install pipx
$ pipx install apolo-all
$ pipx upgrade apolo-all
```

**Installing via pip**

You can also install all of the components through pip.

We highly recommend using the [Anaconda Python 3.8 Distribution](https://www.anaconda.com/distribution/) with default installation settings.

When you have it up and running, run the following commands in Conda Prompt:

```
$ conda install -c conda-forge make
$ conda install -c conda-forge git    
$ pip install -U apolo-cli apolo-extras apolo-flow
$ pip install -U certifi
$ apolo login
```

To make sure that all commands you can find in our documentation work properly, don't forget to run `bash` every time you open Conda Prompt.
{% endtab %}
{% endtabs %}
