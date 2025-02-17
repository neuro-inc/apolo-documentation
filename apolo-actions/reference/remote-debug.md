# Remote Debug

This is an [apolo-flow](https://docs.apolo.us/apolo-flow-reference) action enabling remote debugging. It's intended to be used with Apolo [platform template](https://github.com/neuro-inc/cookiecutter-neuro-project), but can be adapted for other use cases as well.

This action exposes SSH port 22 and maps it to port 2211 used for the PyCharm remote Python debugging feature.

It also expects references to 4 remote folders: for data, config, code, and results. These remotes will be mounted to `/project/data`, `/project/config`, `/project/modules` and `/project/results` respectively.

{% hint style="info" %}
Feel free to check the [Remote Debug action repository](https://github.com/apolo-actions/remote_debug).
{% endhint %}
