# Managing organizations

Organizations provide an additional way to group users.&#x20;

An organization represents a group of people with roles. In this way, it's similar to a cluster, but there are no computational resources behind an organization.

Also, all organization users (including its manager/admin) will use a shared total quota that was set for this specific organization in the cluster.&#x20;

### Creating organizations

You can create a new organization by using the `apolo admin add-org` command:

```
$ apolo admin add-org my-org
```

After this, you can add users to it with the `apolo admin add-org-user` command:

```
$ apolo admin add-org-user my-org alice user
```

Next, an admin or manager of the cluster can add the new organization to the cluster via the  `apolo admin add-org-cluster` command, and then assign quotas within the organization:

```
$ apolo admin add-org-cluster my-cluster my-org
```

Organization managers and admins will have access to all of the resources and users from their organization, but no access to the resources of other organizations or users that aren't members of any organization.

### Assigning managers/admins

An organization will need its own manager or admin who will be able to add new members from this point ion. To assign a cluster's user as an organization admin, use the following command:

```
$ apolo admin add-cluster-user --org ORGANIZATION_NAME CLUSTER_NAME USER_NAME admin
```

An organization manager or admin differs from a cluster manager or admin in that they cannot add people _not on behalf of the organization_ (without the --org parameter).

### Switching organizations

Since a user can be added to the same cluster on behalf of several organizations (as well as directly), it is possible to switch the current organization by using the `apolo config switch-org` command, similarly to how you would switch the current cluster. When an organization is selected, all jobs, files, secrets, disks, etc., will be created within this organization. \
This means that the respective URIs will have the following structure:&#x20;

```
schema://cluster/organization/username/some/path
```

{% hint style="info" %}
You can find more information about organization-related CLI commands in the [corresponding section of our CLI Reference](https://neu-ro.gitbook.io/neu-ro-cli-reference/commands/admin#add-org).
{% endhint %}
