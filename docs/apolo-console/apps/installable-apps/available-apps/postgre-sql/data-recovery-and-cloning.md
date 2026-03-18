# Data recovery and Cloning

## Backups in PostgreSQL application

Starting from v26.2.0 application version, you have a granular control over backup system for PostgreSQL. You can configure frequency and number of backups to keep. You can also rollout database from historical snapshot. Depending on how you use Apolo, you might also have the ability to manually trigger backups and perform in-place data recovery (otherwise, you need to perform database cloning).

On this page, we discuss the following actions that you can perform with the application:

* Data recovery overview
* Cloning the database with various data recovery scenarios
* Triggering backup manually (requires virtual Kubernetes in your Apolo project)

## Restoring data

When it comes to data restore, you have several options:

* PITR (point-int-time-recovery): revert data state to a snapshot before changes occurred&#x20;
* In-Place PITR: performs PITR on a currently running database (potentially, destructive operation)

One can also clone database from backup in object store: deploys a new database instance, seed it with data stored in a backup. This can be combined with the first PITR option.

More details for each of the approaches can be found [here](https://access.crunchydata.com/documentation/postgres-operator/latest/tutorials/backups-disaster-recovery/disaster-recovery).

Within Apolo platform, we allow our users to clone database from available backups, instead of doing in-place PITRs which considered to be a destructive operation.

In case of absolute need, you can also perform in-pace PITRs (if virtual Kubernetes enabled in your project), otherwise, reach out to the support team with the corresponding request.

## PostgreSQL clone

Apolo provides several database cloning options, depending on your data recovery requirements. Each approach involves creating a new PostgreSQL application instance and configuring its data source to use the Blob storage that contains backups of the original PostgreSQL instance.

For instance, you have/had other PostgreSQL application that you want to clone from. This instance should have backups enabled. In it's outputs, you will find the info about backup Bucket: ID,  owner and provider. Note them down, since we it will be needed for cloning.

By specifying `source.pgbackrest_options` input parameter for a new PostgreSQL app, you can control if and how PITR is performed during the database rollout. You can find overview of options [here](https://pgbackrest.org/command.html#command-restore).

Below you will find examples that various cloning strategies and how it influences PostgreSQL app. For each case, you have Apolo platform application configuration and a corresponding `postgrescluster` Kubernetes CRD snippet.

### Clone to latest

Cloning when no PITR is enabled.

{% tabs %}
{% tab title="Apolo application configuration" %}
```yaml
display_name: "Cloned psql"
template_name: postgres
template_version: v26.2.0 / newer
input:
    ...
    source:
        source_bucket:
            id: bucket-a4e0f981-e7b0-40e0-9648-f20b5fe5aee3
            owner: ysem
            bucket_provider: GCP
            credentials: []
        repo1_path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1/
        restore_preset:
            name: cpu-medium
        pgbackrest_options: 
            - --type=default  # Restore to latest WAL
    ...
```
{% endtab %}

{% tab title="Kubernetes CRD" %}
<pre class="language-yaml"><code class="lang-yaml">kind: PostgresCluster
...
spec:
  dataSource:
    pgbackrest:
      stanza: db
      configuration:
      - secret:
<strong>          name: pg-2c5aa2d6a7a74d639d66fa9ea2d40173-pgbackrest-secret
</strong>      global:
        repo1-path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1
      repo:
        name: repo1
        gcs:
          bucket: neuro-pl-3af8740634-org-proj-testbackrestd2aea95b0f68
      options:
      - --type=default  # Restore to latest WAL
...
</code></pre>
{% endtab %}
{% endtabs %}

What it does:

1. Selects the most recent full backup available
2. Applies any differential/incremental backups that follow it
3. Replays ALL available WAL files up to the most recent one in the archive
4. Results in the absolute latest state of your database

Use Case:

* You want the most up-to-date data possible
* Typical disaster recovery scenario
* Cloning production to staging with latest data

Install the database instance, wait till it finishes installation.

Now you have a fresh copy of your database + most recent transactions stored in WAL files in backup object store.

### Clone to snapshot

Cloning from a specified full backup.

{% tabs %}
{% tab title="Apolo application configuration" %}
```yaml
display_name: "Cloned psql"
template_name: postgres
template_version: v26.2.0 / newer
input:
    ...
    source:
        source_bucket:
            id: bucket-a4e0f981-e7b0-40e0-9648-f20b5fe5aee3
            owner: ysem
            bucket_provider: GCP
            credentials: []
        repo1_path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1/
        restore_preset:
            name: cpu-medium
        pgbackrest_options:
            - --type=immediate
            - --set=20251215-145459F   # Restore to exactly this full backup
    ...
```
{% endtab %}

{% tab title="Kubernetes CRD" %}
<pre class="language-yaml"><code class="lang-yaml">kind: PostgresCluster
...
spec:
  dataSource:
    pgbackrest:
      stanza: db
      configuration:
      - secret:
<strong>          name: pg-2c5aa2d6a7a74d639d66fa9ea2d40173-pgbackrest-secret
</strong>      global:
        repo1-path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1
      repo:
        name: repo1
        gcs:
          bucket: neuro-pl-3af8740634-org-proj-testbackrestd2aea95b0f68
      options:
      - --type=immediate
      - --set=20251215-145459F
...
</code></pre>
{% endtab %}
{% endtabs %}

What it does:

1. Restores only the specified backup (e.g., `20251215-145459F`)
2. Does NOT replay any WAL files
3. Stops immediately after the backup is restored
4. Results in database state exactly as it was when that backup finished

Use Case:

* You want a specific snapshot in time
* Creating test datasets from a known backup
* You don't need the latest data, just a consistent snapshot
* Faster restore (no WAL replay)

Install the database instance, wait till it finishes installation.&#x20;

Now you have a database instance rolled out from your **full** (hence suffix **F**) backup performed at `2026-02-13-09:31:31`. All changes made in the database after this backup are not present in clone.

You can also perform a restore from a differential backup. In order to find your backups information, follow the instruction from [#identifying-backups](data-recovery-and-cloning.md#identifying-backups "mention").

### Clone with point-in-time-recovery

PITR allows you to recover your database to the specified timestamp. Imagine some database table was accidentally dropped / truncated, or the migration did not go well. PITR is designed to rescue in such cases.

Before performing PITR:

1. You must have a backup that finished BEFORE your target time - you cannot restore to a time before your first backup. For this, ensure you have configured corresponding backup schedule.
2. All relevant WAL files must be successfully pushed to the repository. To check this, read logs of pgBackRest container.

In Apolo PostgreSQL database application for now, PITR is considered to be used together with database cloning. However, if you need to perform in-place PITR, reach out to support team.

Clone from backup and perform point-in-time-recovery, time is set to specific timestamp `2026-02-14 14:30:00+00`.

{% tabs %}
{% tab title="Apolo application configuration" %}
```yaml
display_name: "Cloned psql"
template_name: postgres
template_version: v26.2.0 / newer
input:
    ...
    source:
        source_bucket:
            id: bucket-a4e0f981-e7b0-40e0-9648-f20b5fe5aee3
            owner: ysem
            bucket_provider: GCP
            credentials: []
        repo1_path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1/
        restore_preset:
            name: cpu-medium
        pgbackrest_options:
            - --type=time
            - --target="2026-02-14 14:30:00+00"   # Restore db state to exactly this time
    ...
```
{% endtab %}

{% tab title="Kubernetes CRD" %}
<pre class="language-yaml"><code class="lang-yaml">kind: PostgresCluster
...
spec:
  dataSource:
    pgbackrest:
      stanza: db
      configuration:
      - secret:
<strong>          name: pg-2c5aa2d6a7a74d639d66fa9ea2d40173-pgbackrest-secret
</strong>      global:
        repo1-path: /pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1
      repo:
        name: repo1
        gcs:
          bucket: neuro-pl-3af8740634-org-proj-testbackrestd2aea95b0f68
      options:
      - --type=time
      - --target="2026-02-14 14:30:00+00"
...
</code></pre>
{% endtab %}
{% endtabs %}

What it does:

1. Automatically selects the most recent full backup completed before `2026-02-14 14:30:00+00`
2. Applies any differential/incremental backups from that backup set (if they're also before the target time)
3. Replays WAL files from the last backup up to exactly `2026-02-14 14:30:00+00`
4. Stops recovery at the precise moment specified (down to the second)
5. Results in database state exactly as it was at 2026-02-14 14:30:00 UTC

Use Case:

* Recovery from accidental data deletion/corruption (restore to just before the incident)
* Compliance/audit requirements (restore database to specific point for investigation)
* Testing "what-if" scenarios (see database state at a particular business moment)
* Recovering from a failed deployment/migration (go back to before the change)
* More precise than a backup snapshot, but slower than `--type=immediate` (due to full WAL replay)

Install the database instance, wait till it finishes installation.

Now you have a database instance rolled out from your **full** backup performed before  `2026-02-14 14:48:00+00` + all transactions happened between full backup capture and specified time are reapplied. Every change that happened after this time is not present in the database clone.

## Triggering manual backup

{% hint style="warning" %}
Currently, you can only perform this action if your Apolo project has virtual Kubernetes enabled, since you need to have a direct access to the virtual cluster with the application installed.

If you don't have virtual Kubernetes enabled, but still need to perform ad-hoc backup, contact the support team.
{% endhint %}

To trigger a manual backup of PostgreSQL cluster perform following actions:

1. Identify your `postgrescluster` CRD name. For this, use your virtual Kubernetes credentials and perform `kubectl get postgrescluster`. Sample output:

```
kubectl get postgrescluster                                                                
NAME                                  AGE
pg-2c5aa2d6a7a74d639d66fa9ea2d40173   4d4h
```

2. Enable manual backups by patching `postgrescluster` CRD. Add the following configuration:

```yaml
spec:
  backups:
    pgbackrest:
      manual:
        repoName: repo1
        options:
         - --type=full
```

This instructs pgBackRest to perform a full backup of your database into the previously configured backup repo (repo1, added by Apolo during app installation).

3. Trigger backup by adding annotation to your `postgrescluster` CRD

```
kubectl annotate postgrescluster pg-2c5aa2d6a7a74d639d66fa9ea2d40173 postgres-operator.crunchydata.com/pgbackrest-backup="$(date)" --overwrite
```

4. Check that backup job started and completed successfully:

```
kubectl get po -l postgres-operator.crunchydata.com/cluster=pg-2c5aa2d6a7a74d639d66fa9ea2d40173,postgres-operator.crunchydata.com/pgbackrest-backup=manual
NAME                                                    READY   STATUS      RESTARTS   AGE
pg-2c5aa2d6a7a74d639d66fa9ea2d40173-backup-p2dc-69t5r   0/1     Completed   0          9m
```

This is it, you manual backup data will be stored in the corresponding backup bucket.

## Identifying backups

Overall goal here is to configure and run dedicated pgbackrest tool within a job. The configuration approach highly depends on what backup object store you use. In Apolo you have multiple [object store options](../../../pre-installed/buckets.md) and here are highlights for most common of them. Consult pgbackrest [documentation](https://pgbackrest.org/configuration.html#section-repository) if if you cannot find needed example here.

Configuring **pgbackrest**:

{% tabs %}
{% tab title="AWS/Minio bucket" %}
1. Create dedicated credentials for backup bucket via `apolo blob mkcredentials <bucket>`
2. Store access key id  and secret access key and to access the bucket as secrets:
   1. `apolo secret add KEY_ID <access_key_id from mkcredentials>`
   2. `apolo secret add SECRET_KEY <secret_access_key from mkcredentials>`&#x20;
3. Run job mounting those secrets as pgbackrest's repo1 config params:

```
apolo run \
    -e PGBACKREST_REPO1_S3_KEY=secret:KEY_ID \
    -e PGBACKREST_REPO1_S3_KEY_SECRET=secret:SECRET_KEY \
    ubuntu -- bash
```
{% endtab %}

{% tab title="GCS bucket" %}
1. Create dedicated credentials for backup bucket via `apolo blob mkcredentials <bucket>`
2. Store credentials as secret: `base64 -d` data from `key_data` and put it into the `REPO_ACCESS_DATA` secret:

`echo <key_data_from_mkcredentials> | base64 -d > key_data && apolo secret add REPO_ACCESS_DATA @key_data`&#x20;

3. Attach this secret key file `apolo run -v secret:GCS_KEY_DATA:/tmp/creds ubuntu -- bash`&#x20;
{% endtab %}
{% endtabs %}

3. Run a job with credentials attached from instructions above
4. Within job: install **pgbackrest** tool, also install ca-certificates

```
apt-get update -qq
DEBIAN_FRONTEND=noninteractive apt-get -y install pgbackrest ca-certificates
```

5. Configure pgbackrest tool:

{% tabs %}
{% tab title="AWS/Minio" %}
```bash
mkdir /etc/pgbackrest
cat > /etc/pgbackrest/pgbackrest.conf << 'EOF'
[global]
repo1-type=s3
repo1-path=/pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1
repo1-s3-endpoint=https://blob.imdc.org.apolo.us
repo1-s3-region=minio
repo1-s3-uri-style=path
repo1-s3-bucket=neuro-pl-fdcffc56db-tubesupply-development-neef8a2d36536d
EOF
```

Note: S3 Endpoint, region and bucket name are displayed in the output of `mkcredentials` command.
{% endtab %}

{% tab title="GCP" %}
```bash
mkdir /etc/pgbackrest
cat > /etc/pgbackrest/pgbackrest.conf << 'EOF'
[global]
repo1-type=gcs
repo1-path=/pgbackrest/platform--org--proj--17a7adb4a97c5d671ab4ec4e/pg-2c5aa2d6a7a74d639d66fa9ea2d40173/repo1
repo1-gcs-bucket=neuro-pl-3af8740634-org-proj-testbackrestd2aea95b0f68
repo1-gcs-key=/tmp/creds
[db]
pg1-path=/pgdata
EOF
```
{% endtab %}
{% endtabs %}

Note: ensure repo1-path is correct by confirming via `apolo blob ls blob:<bucketURI>/...` . It should contain `archive/` and `backup/` subpaths. Bucket name is the name in source system, reported to you during bucket creation or via `apolo blob statbucket` command.

6. List backups with `pgbackrest info` command. Sample output:

```bash
root@job-1f581229-77a3-4f81-aa5f-e567aa006427:/# pgbackrest info
stanza: db
    status: ok
    cipher: none

    db (current)
        wal archive min/max (16): 000000010000000000000001/000000010000000000000014

        full backup: 20260212-191450F
            timestamp start/stop: 2026-02-12 19:14:50+00 / 2026-02-12 19:17:44+00
            wal start/stop: 000000010000000000000006 / 000000010000000000000008
            database size: 55.7MB, database backup size: 55.7MB
            repo1: backup set size: 5.2MB, backup size: 5.2MB

        full backup: 20260213-093131F
            timestamp start/stop: 2026-02-13 09:31:31+00 / 2026-02-13 09:34:31+00
            wal start/stop: 000000010000000000000012 / 000000010000000000000013
            database size: 56.5MB, database backup size: 56.5MB
            repo1: backup set size: 5.3MB, backup size: 5.3MB
```

With this information you can clone your database and perform PITR / or specific backup rollout.

## Summary

Apolo's PostgreSQL application backup support provides:

* **Flexibility:** Choose between latest data, specific snapshots, or precise timestamps
* **Safety:** Clone-based approach prevents accidental data loss
* **Compliance:** Meet audit requirements with point-in-time recovery
* **Control:** Manual triggers for critical moments, automated schedules for routine protection
* **Speed Options:** Trade-off between restore speed and data freshness based on your needs

## References

* [Managing Apolo PostgreSQL application via web console](./)
* [Managing Apolo PostgreSQL application via CLI](../../../../../apolo-concepts-cli/apps/installable-apps/available-apps/postgresql.md)
* [Underlying Crunchy PostgreSQL for Kubernetes disaster & recovery guidelines](https://access.crunchydata.com/documentation/postgres-operator/latest/tutorials/backups-disaster-recovery/disaster-recovery#-clone-from-backups-stored-in-s3--gcs--azure-blob-storage)
* [Restore command spec from pgBackRest](https://pgbackrest.org/command.html#command-restore)
