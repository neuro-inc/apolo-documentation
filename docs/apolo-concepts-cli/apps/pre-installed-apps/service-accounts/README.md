---
description: Non-human identities for automation, integrations and external clients
---

# Service Accounts

## Overview

A service account is a non-human identity that belongs to your Apolo user. It is meant for cases where a token, rather than a person, needs to talk to the platform:

* CI/CD pipelines and automation scripts.
* Third-party integrations and API clients.
* Collaborators or customers outside of your Apolo organization who need access to a specific set of your resources.

Every service account is created with a default cluster, organization and project, and is backed by a **role** of the form `<owner>/service-accounts/<name>`. That role is the principal you grant permissions to — the service account itself has no access to your resources until you share something with its role.

{% hint style="info" %}
Service accounts can be managed on the [Service Accounts](../../../../apolo-console/apps/pre-installed/service-accounts.md) page in the Apolo Console as well as through the Apolo CLI and the platform API. This page documents the CLI workflow.
{% endhint %}

To hand a service account to someone outside of Apolo, see [Sharing access with external clients](sharing-access-with-external-clients.md).

## Managing service accounts

### Create

```bash
apolo service-account create --name my-client
```

```
 Id               service-account-efa6ee98-edbf-42a9-bc0d-bb8f954a4db7
 Name             my-client
 Role             alice/service-accounts/my-client
 Owner            alice
 Default cluster  default
 Default org      apolo
 Default project  apoloproject
 Created at       a moment ago
```

The default cluster, organization and project are taken from your current context and can be overridden:

```bash
apolo service-account create \
  --name my-client \
  --default-cluster default \
  --default-org apolo \
  --default-project apoloproject
```

These defaults only decide which context the account lands in when it logs in — they grant nothing on their own.

### List and inspect

```bash
apolo service-account ls
```

```
  Id                                                     Name        Role                              Default cluster   Created At
 ─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
  service-account-efa6ee98-edbf-42a9-bc0d-bb8f954a4db7   my-client   alice/service-accounts/my-client   default          5 minutes ago
```

`apolo service-account get <id-or-name>` prints the same fields as `create`, without the token. Use it when you need to look up the backing role for an ACL grant.

### Remove

```bash
apolo service-account rm my-client
```

Removing a service account invalidates its token immediately. Any client still using it starts receiving `401 Unauthorized`.

## The token

`apolo service-account create` prints the token **once**, in two forms:

* **Full token with cluster and API URL embedded.** Use it as the `APOLO_PASSED_CONFIG` environment variable — a client with this value needs no prior `apolo login`, since the cluster and API URL travel with the token.
* **Just auth token.** Use it with `apolo config login-with-token`, as a `Bearer` token against the platform API, and as the password for `docker login`.

{% hint style="danger" %}
The token cannot be retrieved later. Store it in a secret manager as soon as it is created, and deliver it to its consumer over a secure channel — never over email or chat, and never in a Git repository.

To rotate a token, remove the service account and create a new one, then re-apply its grants.
{% endhint %}

## Permissions

A newly created service account can see nothing of yours. Access is granted to its **backing role**, using the same ACL model as sharing with a human user:

```bash
apolo acl grant image:my-image alice/service-accounts/my-client read
```

Permission levels are `read`, `write` and `manage`, and they are inclusive — `write` implies `read`, `manage` implies both. Grants can be listed with `apolo acl ls --shared` and withdrawn with `apolo acl revoke`. Removing the account removes its grants along with it.

Because the service account is a role principal, everything in the [sharing topic](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-sharing) applies to it, including grouping several resources behind a custom role.

[Sharing access with external clients](sharing-access-with-external-clients.md) works through this end to end, including the URI forms to use and how a client consumes the access.

## References

* [Apolo CLI: service-account command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/service-account)
* [Apolo CLI: acl command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/acl)
* [Apolo CLI: using the sharing functionality](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-sharing)
* [Apolo SDK: service accounts reference](https://apolo-sdk.readthedocs.io/latest/service_accounts_reference.html)
* [platform-service-accounts-api](https://github.com/neuro-inc/platform-service-accounts-api/)
