---
description: Give a collaborator outside of Apolo access to your registry or the API
---

# Sharing access with external clients

## Overview

Sometimes a collaborator, a customer, or an automated system outside of your Apolo organization needs a narrow slice of your resources — the container images you publish for them, or API access to the jobs they operate — without becoming a member of your organization.

A [service account](./) covers this. You create one, grant its backing role exactly the resources the client needs, and hand over the token. The client never sees the rest of your project, and you can withdraw access at any time without touching your own credentials.

This page walks the flow end to end.

## 1. Create a service account for the client

Create one account per client, so that each can be revoked independently:

```bash
apolo service-account create --name acme-corp
```

Take note of two things from the output:

* the **Role**, e.g. `alice/service-accounts/acme-corp` — this is what you grant permissions to;
* the **token**, in both of its forms — this is what you hand to the client. It is shown only once.

## 2. Grant only what the client needs

Grants go to the backing role. Within your current cluster, organization and project, a short URI is enough:

```bash
apolo acl grant image:my-image alice/service-accounts/acme-corp read
```

To grant something outside of the current context, spell the URI out in full:

```bash
apolo acl grant image://default/apolo/apoloproject/my-image alice/service-accounts/acme-corp read
```

{% hint style="warning" %}
`image:/<project>/<name>` is not the same as `image://<cluster>/<org>/<project>/<name>`. The single-slash form resolves `<project>` against your current cluster and organization, so passing an organization name there silently addresses the wrong resource and the grant fails with `Not enough permissions`. When in doubt, use the full URI.
{% endhint %}

The same works for the other resource types:

```bash
apolo acl grant storage:datasets/public alice/service-accounts/acme-corp read
apolo acl grant job:my-job alice/service-accounts/acme-corp write
apolo acl grant secret:api-key alice/service-accounts/acme-corp read
```

Grant `read` unless the client genuinely needs to write. `write` includes deletion, and `manage` lets the holder re-share the resource with others.

If a client needs many resources, collect them behind a custom role once and grant that role instead:

```bash
apolo acl add-role alice/roles/acme-corp
apolo acl grant image:my-image alice/roles/acme-corp read
apolo acl grant storage:datasets/public alice/roles/acme-corp read
apolo acl grant role://alice/roles/acme-corp alice/service-accounts/acme-corp read
```

Review what you have shared at any time:

```bash
apolo acl ls --shared
```

```
  image:my-image                    read    alice/service-accounts/acme-corp
```

## 3. Hand over the token

Deliver the token through a secret manager or another secure channel. Tell the client which of the two forms you are giving them — they are not interchangeable:

| Token form | Contains | Used for |
| ---------- | -------- | -------- |
| Full token | auth token + cluster + API URL | `APOLO_PASSED_CONFIG` |
| Auth token | auth token only | `apolo config login-with-token`, `Authorization: Bearer`, `docker login` |

## 4. Client side: using the Apolo API

### With the Apolo CLI

The client installs the [Apolo CLI](../../../../apolo-concepts-cli/installing.md) and logs in with the auth token:

```bash
apolo config login-with-token <auth-token> https://api.<cluster-domain>/api/v1
```

```
Logged into https://api.<cluster-domain>/api/v1 as alice/service-accounts/acme-corp,
current cluster is default, org is apolo project is apoloproject
```

The cluster, organization and project come from the defaults the account was created with — the client lands straight in the right context.

For CI jobs and other non-interactive environments, the full token removes the login step entirely — the cluster and API URL are already inside it:

```bash
export APOLO_PASSED_CONFIG=<full-token>
apolo acl ls
```

The client can confirm what they received with `apolo acl ls`, which lists exactly the resources you granted.

### Without the Apolo CLI

The auth token is a bearer token for the platform API, so any HTTP client works:

```bash
curl -H "Authorization: Bearer <auth-token>" \
  https://api.<cluster-domain>/api/v1/jobs
```

The same endpoint without the header answers `401 Unauthorized`, which makes it a convenient way for the client to confirm the token was delivered intact.

## 5. Client side: using the image registry

### With the Apolo CLI

```bash
apolo config docker
apolo image pull image:my-image:v1.0
```

`apolo config docker` registers the `docker-credential-apolo` helper in the client's Docker configuration, so `docker` picks up the platform credentials automatically.

### With plain Docker

A client that has Docker but not the Apolo CLI authenticates with the literal username `token` and the auth token as the password:

```bash
echo "<auth-token>" | docker login registry.api.<cluster-domain> -u token --password-stdin
docker pull registry.api.<cluster-domain>/<org>/<project>/my-image:v1.0
```

Note that the registry path has no cluster segment — it is `<org>/<project>/<image>`, and the cluster is determined by the registry host.

Access is enforced per image. An image that was not granted fails to pull:

```
unexpected status from HEAD request to
https://registry.api.<cluster-domain>/v2/<org>/<project>/other-image/manifests/latest: 403 Forbidden
```

and a `read` grant does not allow pushing:

```
unexpected status from PUT request to
https://registry.api.<cluster-domain>/v2/<org>/<project>/my-image/manifests/v2.0: 403 Forbidden
```

## 6. Revoke access

To withdraw a single resource while keeping the account alive:

```bash
apolo acl revoke image:my-image alice/service-accounts/acme-corp
```

To end the engagement entirely, remove the service account. Its token stops working immediately:

```bash
apolo service-account rm acme-corp
```

## Best practices

* One service account per client or per pipeline, so access can be withdrawn without collateral damage.
* Grant `read` by default; escalate only when a concrete task requires it.
* Scope defaults (`--default-cluster`, `--default-org`, `--default-project`) to where the client will actually work.
* Store tokens in a secret manager, never in a repository, an image layer, or a chat message.
* Rotate by recreating the account and re-applying its grants — tokens cannot be regenerated in place.
* Audit periodically with `apolo acl ls --shared` and `apolo service-account ls`.

## References

* [Service Accounts](./)
* [Apolo CLI: service-account command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/service-account)
* [Apolo CLI: acl command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/acl)
* [Apolo CLI: using the sharing functionality](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-sharing)
* [Apolo CLI: config command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/config)
* [Images](../images.md)
