---
description: Apolo secrets management
---

# Secrets

## Overview

The Secrets app in the Apolo Console allows users to securely store and manage sensitive data, such as API tokens, passwords, configuration values or files associated with their projects.

Secrets ensure that sensitive information remains protected while being accessible only to workloads of an authorized users within your project.

The Secrets app, accessible from the left-hand navigation menu, provides a centralized interface to:

* Create and manage secure key-value pairs.
* View a list of existing secrets in your project.
* Delete secrets that are no longer required.

{% hint style="info" %}
Once the secret is created, you can not read it's value via Apolo Console, Apolo CLI or update it.

You can only delete the existing secret and create a new one.

When you remove the secret, the currently running workloads will stay intact even if you recreate the secret.
{% endhint %}

To learn more about secrets management via Apolo CLI visit a [dedicated documentation](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/secret) page.

If you are interested in secrets usage with Apolo Jobs, please check [this](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-secrets) page.

The use of secrets in Flows is the same as in the CLI. Simply reference your secret name in environment variable value or in volume mount configuration sections of tasks or volumes. For more details, check our [Flow documentation](https://app.gitbook.com/s/-MMLOF_FqiWBMcOdY8cj/workflow-syntax/live-workflow-syntax).&#x20;

## References

* [Apolo CLI: Secrets management](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/secret)
* [Apolo CLI: Secrets usage](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-secrets)
* [Apolo Flow: configuration file reference](https://app.gitbook.com/s/-MMLOF_FqiWBMcOdY8cj/workflow-syntax)

