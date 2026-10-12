# Service Accounts

## Overview

Service Accounts is a pre-installed app for creating non-human identities in Apolo.

Use a service account when you need to grant access without using a personal user account. This is useful for CI/CD pipelines, API clients, and collaborators outside Apolo.

Service accounts are managed in Apolo. Access is granted through ACLs and roles.

**Use an Apolo Service Account token generated via Apolo CLI or Apolo SDK.**

The token is returned during creation and must be stored securely. It is only available at creation time.

The Apolo SDK returns a `(ServiceAccount, token)` pair from `ServiceAccounts.create(...)`.

The Apolo CLI prints two token forms when you run `apolo service-account create`:

* a full passed-config token, which can be used as `APOLO_PASSED_CONFIG`
* a raw auth token, which can be passed to `apolo config login-with-token`

Each service account is backed by an authorization role. The role is shown in `apolo service-account ls` and `apolo service-account get`.

## When to use

Use a service account when:

* An external collaborator needs access to image registry resources.
* A client needs API access from outside Apolo.
* An automation workflow needs access to Apolo resources.
* You need a dedicated identity for a third-party service.

For a practical flow that covers creating a service account, granting access, and sharing the token with an external client, see [Sharing access with external clients](https://docs.apolo.us/index/apolo-console/apps/pre-installed/service-accounts/sharing-access-with-external-clients).

## Management / Usage

Use the `apolo service-account` command group to manage service accounts.

### Create a service account

```bash
apolo service-account create --name <name>
```

Optional defaults:

* `--default-cluster`
* `--default-org`
* `--default-project`

If you do not set these values, Apolo uses the current cluster, org, and project.

`create` returns the service account info and its token. The token is shown only during creation.

In quiet mode, `apolo service-account create` prints the token value only.

The generated token embeds the target cluster and, if present, the org and project context.

### List service accounts

```bash
apolo service-account ls
```

Lists your service accounts.

### Get a service account

```bash
apolo service-account get <service-account>
```

Shows service account details by ID or name.

### Remove a service account

```bash
apolo service-account rm <service-account>
```

Removes the service account and revokes its token.

## Permissions and sharing

Service accounts use the same ACL model as other Apolo resources.

Apolo supports five access levels:

* `deny`
* `list`
* `read`
* `write`
* `manage`

Permissions are inclusive. `write` includes `read`, and `manage` includes `read` and `write`.

Use ACLs to grant access to the resources that a service account needs.

Grant resource access to the service account's backing role principal, not to the service account id itself.

```bash
apolo acl grant image:<image-name> <service-account-role> read
apolo acl grant storage:/<path> <service-account-role> read
apolo acl revoke image:<image-name> <service-account-role>
```

For grouped access, use a role:

```bash
apolo acl add-role <owner>/roles/<role-name>
apolo acl grant image:<image-name> <owner>/roles/<role-name> read
```

You can inspect shared resources with:

```bash
apolo acl ls --shared
```

## Security recommendations

* Create one service account per client or integration.
* Grant only the minimum required access.
* Prefer `read` for read-only workflows.
* Use `write` only when the client must modify resources.
* Store the token in a secure secret store.
* Revoke access when the integration is no longer needed.
* Remove the service account when you no longer need it.

## References

* [service-account CLI Reference](https://docs.apolo.us/index/apolo-cli/commands/service-account)
* [Using the sharing functionality](https://docs.apolo.us/index/apolo-cli/topics/topic-sharing)
* [acl CLI Reference](https://docs.apolo.us/index/apolo-cli/commands/acl)
* [Sharing access with external clients](https://docs.apolo.us/index/apolo-console/apps/pre-installed/service-accounts/sharing-access-with-external-clients)
* [ServiceAccounts API Reference](https://apolo-sdk.readthedocs.io/latest/service_accounts_reference.html)
* [platform-service-accounts-api](https://github.com/neuro-inc/platform-service-accounts-api/)

