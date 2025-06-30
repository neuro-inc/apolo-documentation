# PostgreSQL

## Overview

PostgreSQL is a powerful, open-source relational database system known for its reliability, feature richness, and extensibility. A detailed description of this application at the dedicated Apolo Console's  [postgre-sql.md](../../../apolo-console/apps/available-apps/postgre-sql.md "mention") page.

## Managing application via Apolo CLI

**Step 1** — use CLI command to get application configuration file template:

`apolo app-template get postgres -o mypostgres.yaml`&#x20;

**Step 2** —  fill in application parameters. Here is an example config file with some of those parameters:

```yaml
# Example of mypostgres.yaml

template_name: postgres
template_version: v25.5.0
display_name: mypostgres
input:
  backup:
    enable: true
  pg_bouncer:
    preset:
      name: cpu-medium
    replicas: 1
  postgres_config:
    db_users:
    - db_names:
      - dbone
      - dbtwo
      name: userone
    - db_names:
      - dbtwo
      name: usertwo
    instance_replicas: 3
    instance_size: 10
    postgres_version: '16'
  preset:
    name: cpu-medium
```

Debrief of configuration parameters:

1. We enabled incremental and full backups via setting `input.backup.enable=true` .
2. We configured PG Bouncer to have 1 replica and use `cpu-medium` resource preset in runtime.
3. We created two users: `userone` and `usertwo` . We also created two databases: `dbone` , `dbtwo` . Afterwards we granted `userone` access to `dbone` and `dbtwo` DBs, while `usertwo` has access only to `dbtwo` . The users have write access level for those databases.
4. We also setup PostgreSQL cluster to work in HA mode using Patroni and have 3 server replicas by setting `input.postgres_config.instance_replicas=3` . The resource preset for each instance is set to be `cpu-medium` via `inputs.preset.name=cpu-medium` configuration parameter.
5. The disk size under each replica is 10 Gigs.
6. The PostgreSQL server version is 16.

**Step 3** — now you can install this application into your Apolo project:

`apolo app install -f mypostgres.yaml`&#x20;

You could see the application status transitions via CLI:

```
apolo app list
  ID                                     Name                                                          Display Name                      Template                          Version   State     
 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 
  70565e06-69e9-4cbd-a895-616a1a61454a   apolo-apoloproject-postgres-70565e06                          mypostgres                        postgres                          v25.5.0   healthy   
  ...
```

If you want to see logs of the application, hit `apolo app logs <app-id>` supplying the ID from a previous command output. _Note:_ loading and displaying logs for your application might take some time.

**Step 4** — if you want to remove the application via CLI, use `apolo app remove <app-id>`.

You can find more information for application management commands in the list of Apolo CLI references below.

## References

* [Apolo PostgreSQL application](../../../apolo-console/apps/available-apps/postgre-sql.md)
* [Apolo application template management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app-template)
* [Apolo application management commands](https://app.gitbook.com/s/-MOkWy7dB5MDbkSII8iF/commands/app)
