# Sharing access with external clients

## Overview

Use a dedicated service account for each external client or collaborator.

The standard flow is:

1. Create a service account in Apolo.
2. Grant only the required access with ACLs or roles.
3. Share the generated token through a secure channel.
4. Verify access.
5. Revoke access when it is no longer needed.

The token is generated in Apolo. It is not created in GitBook.

Use the service account token that Apolo returns during creation. For CLI login, use the raw auth token with `apolo config login-with-token`. To pass configuration through the environment, use the full passed-config token as `APOLO_PASSED_CONFIG`.

## Share image registry access

For read-only image registry access:

1. Create a service account.
2. Grant `read` access to the image resource through the service account's backing role principal.
3. Share the token with the external client.
4. Ask the client to use the token for image access.
5. Revoke the grant or remove the service account when the collaboration ends.

Example:

```bash
apolo service-account create --name client-image-access
apolo acl grant image:<image-name> <service-account-role> read
apolo acl ls --shared image://
```



## Share API access

For API access:

1. Create a dedicated service account.
2. Grant the minimal required resource access to the service account's backing role principal.
3. Use a role if the service account needs access to multiple resources.
4. Share the token securely.
5. Revoke the access once the client no longer needs it.

Example:

```bash
apolo service-account create --name client-api-access
apolo acl add-role <owner>/roles/client-api-access
apolo acl grant image:<image-name> <owner>/roles/client-api-access read
apolo acl grant storage:/<path> <owner>/roles/client-api-access read
```

## Role-based sharing

Roles group permissions for multiple resources.

Use a role when the service account needs access to more than one entity. This keeps the permissions easier to audit and revoke.

## Verify access

Use `apolo acl ls` and `apolo acl ls --shared` to verify what is shared and which access level is granted.

Use `apolo service-account get <service-account>` to inspect the service account's backing role and defaults.

Use `apolo acl list -u <service-account-role>` to inspect the permissions attached to the service account role.

For Docker Engine or Docker CLI clients, the external client can use the service account token to authenticate to the Apolo registry and then pull the image:

```bash
docker login <apolo-registry-host>
# Username: <service-account-name>
# Password: <service-account-token>
docker pull <apolo-registry-host>/<image-name>:<tag>
```

## Revoke access

Revoke access with `apolo acl revoke` or remove the service account with `apolo service-account rm`.

## References

* [service-account CLI Reference](https://docs.apolo.us/index/apolo-cli/commands/service-account)
* [Using the sharing functionality](https://docs.apolo.us/index/apolo-cli/topics/topic-sharing)
* [acl CLI Reference](https://docs.apolo.us/index/apolo-cli/commands/acl)
* [ServiceAccounts API Reference](https://apolo-sdk.readthedocs.io/latest/service_accounts_reference.html)

