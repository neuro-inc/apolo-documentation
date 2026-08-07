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

For a resource in another project of the same cluster and organization, use the single-slash form, whose first segment is the **project**:

```bash
apolo acl grant image:/other-project/my-image alice/service-accounts/acme-corp read
```

To cross clusters or organizations, spell the URI out in full:

```bash
apolo acl grant image://other-cluster/other-org/other-project/my-image alice/service-accounts/acme-corp read
```

{% hint style="warning" %}
In the single-slash form the first segment is the project, not the organization — your current organization is always prepended for you. Writing `image:/<org>/<project>/<name>` therefore resolves to `image://<cluster>/<org>/<org>/<project>/<name>`, which addresses nothing, and the grant fails with the misleading `Not enough permissions (Forbidden)` rather than a not-found error. When in doubt, check what a URI resolves to with `apolo acl ls --full-uri`.
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

{% hint style="info" %}
`apolo acl ls --shared` reports the principal you granted to. When access is inherited through a custom role, the service account will not appear here — the role will. To see everything one account can actually reach, ask about it directly:

```bash
apolo acl ls -u alice/service-accounts/acme-corp
```
{% endhint %}

## 3. Hand over the token

Deliver the token through a secret manager or another secure channel. Tell the client which of the two forms you are giving them — they are not interchangeable:

| Token form | Contains | Used for |
| ---------- | -------- | -------- |
| Full token | auth token + cluster + API URL | `APOLO_PASSED_CONFIG` |
| Auth token | auth token only | `apolo config login-with-token`, `Authorization: Bearer`, `docker login` |

## 4. Client side: using the Apolo API

### With the Apolo CLI

The client installs the [Apolo CLI](../../../../apolo-concepts-cli/installing.md) and logs in with the auth token. The API URL is the one you see in your own `apolo config show` output — pass it to the client along with the token:

```bash
apolo config login-with-token <auth-token> https://api.apolo.example.com/api/v1
```

```
Logged into https://api.apolo.example.com/api/v1 as alice/service-accounts/acme-corp,
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
  https://api.apolo.example.com/api/v1/jobs
```

This is a good way for the client to confirm the token arrived intact: with a valid token the call returns `200 OK`, and a truncated or expired one returns `401 Unauthorized`. Pick an endpoint that is actually authenticated for this check — some paths answer `200` regardless of the header and prove nothing.

## 5. Client side: using the image registry

First, look up the registry host and give it to the client. It comes from the cluster configuration and is **not** derived from the API URL, so it has to be read rather than guessed — `apolo config show` prints it as `Docker Registry URL`.

### With the Apolo CLI

```bash
apolo image pull image:my-image:v1.0
```

That is the whole step: the CLI authenticates the pull itself, so no `docker login` and no `apolo config docker` are needed. Run `apolo config docker` only if the client also wants to use the plain `docker` command against the registry — it registers the `docker-credential-apolo` helper in their Docker configuration so `docker` picks up the platform credentials automatically.

### With plain Docker

A client that has Docker but not the Apolo CLI authenticates with the auth token as the **password**. The username is not checked — `token` is the value the Apolo credential helper sends, so it is the conventional choice:

```bash
echo "<auth-token>" | docker login registry.apolo.example.com -u token --password-stdin
docker pull registry.apolo.example.com/<org>/<project>/my-image:v1.0
```

The registry path has no cluster segment — the cluster is determined by the registry host. On a cluster without organizations the `<org>` segment is dropped too, leaving `<project>/<image>`; `apolo image ls --full-uri` in the owner's context shows which shape applies.

Access is enforced per image. An image that was not granted fails to pull:

```
unexpected status from HEAD request to https://registry.apolo.example.com/v2/<org>/<project>/other-image/manifests/latest: 403 Forbidden
```

and a `read` grant does not allow pushing:

```
unexpected status from PUT request to https://registry.apolo.example.com/v2/<org>/<project>/my-image/manifests/v2.0: 403 Forbidden
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
* Audit periodically with `apolo service-account ls`, `apolo acl ls --shared`, and `apolo acl ls -u <role>` for anything granted through a custom role.

## References

* [Service Accounts](./)
* [Apolo CLI: service-account command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/service-account)
* [Apolo CLI: acl command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/acl)
* [Apolo CLI: using the sharing functionality](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/topics/topic-sharing)
* [Apolo CLI: config command](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/config)
* [Images](../images.md)
