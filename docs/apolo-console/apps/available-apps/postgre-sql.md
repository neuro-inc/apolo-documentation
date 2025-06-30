# PostgreSQL

## Overview

PostgreSQL is a powerful, open-source relational database system known for its reliability, feature richness, and extensibility. It supports advanced SQL compliance, transactional integrity, and scalability, making it a popular choice for both OLTP and OLAP workloads. PostgreSQL’s flexibility allows for use across a wide range of applications, from web services to large-scale data platforms. Apolo uses the **Crunchy Data distribution of PostgreSQL**, which builds on the core database engine by adding production-grade features such as high availability, automated backups, Kubernetes-native deployment, and enhanced monitoring. This ensures that PostgreSQL on Apolo is robust, secure, and ready for mission-critical workloads in modern cloud environments.

**PostgreSQL** comes with integrated **PgBouncer,** which sits between your application and PostgreSQL, managing and reusing database connections to reduce overhead and improve performance—especially in high-concurrency environments.

## Key Features <a href="#key-features" id="key-features"></a>

* **Enterprise-Grade PostgreSQL**: Fully open-source and compliant with PostgreSQL, offering advanced tooling and support for critical workloads.
* **High Availability**: Built-in support for HA via Patroni, Kubernetes-native failover handling, and synchronous streaming replication.
* **Automated Backups & Point-in-Time Recovery (PITR)**: Integrated tools for automated scheduled backups, WAL archiving, and recovery to any point.
* **Monitoring & Metrics**: Native integration with Prometheus and Grafana for visibility into database performance and health.
* **Security & Compliance**: Includes features such as TLS encryption, role-based access control, audit logging, and SELinux hardening.
* **Kubernetes-Native**: Optimized for Kubernetes through Crunchy Data’s PostgreSQL Operator (PGO), enabling declarative management and scalable deployments.

## Installing <a href="#installation-and-deployment-on-apolo" id="installation-and-deployment-on-apolo"></a>

As with other applications on Apolo, you can deploy Crunchy PostgreSQL cluster via web console, or using Apolo CLI.

### **Highlights of the installation flow**

* **Resource presets**: select the resource preset (e.g. `cpu-medium`, `cpu-large`) that defines hardware requirements (CPU, memory, etc.) for the PostgreSQL server or PG bouncer.
* **Persistent Storage Management**: we automatically provisions high-performance, durable storage with snapshot and backup support.
* **Backup & PITR Support**: we integrate your setup with Apolo buckets to configure backup system for regular snapshots and point-in-time recovery options.
* **RBAC**: you can preconfigure list of databases and users, define user to database access grid.

***

### Install via Apolo web console

Below is a brief description of how to deploy the PostgreSQL application with exactly the same configuration via Apolo web console.

**Step 1** — find PostgreSQL application in list of _apps,_ and click "install" button.

**Step 2 —** fill in the same parameters for this application

{% tabs %}
{% tab title="PostgreSQL parameters 1" %}
<figure><img src="../../../.gitbook/assets/image (9) (1).png" alt=""><figcaption><p>Configuring server instance preset, number of server instances, PostgreSQL version</p></figcaption></figure>
{% endtab %}

{% tab title="PostgreSQL parameters 2" %}
<figure><img src="../../../.gitbook/assets/image (11) (1).png" alt=""><figcaption><p>Configure disk size for each instance, first user and it's access to databases</p></figcaption></figure>
{% endtab %}

{% tab title="PostgreSQL parameters 3" %}
<figure><img src="../../../.gitbook/assets/image (12) (1).png" alt=""><figcaption><p>Configure second user to have access only to <code>dbtwo</code> database</p></figcaption></figure>
{% endtab %}

{% tab title="PostgreSQL parameters 4" %}
<figure><img src="../../../.gitbook/assets/image (13) (1).png" alt=""><figcaption><p>Configure PG Bouncer resource preset, enable backup configuration and configure app name.</p></figcaption></figure>
{% endtab %}
{% endtabs %}

After setting up all input parameters, click "install" to start the installation. You will be redirected to an application details page, which displays application inputs, outputs and health status.

<figure><img src="../../../.gitbook/assets/image (15) (1).png" alt=""><figcaption><p>Application details page</p></figcaption></figure>

Your PostgreSQL cluster is ready. The application outputs, including access credentials are displayed below at this screen too. You could utilize this outputs with other applications or other workloads.&#x20;

<figure><img src="../../../.gitbook/assets/image (17) (1).png" alt=""><figcaption><p>PostgreSQL app outputs</p></figcaption></figure>

You could remove this application just like all other apps by clicking the "Uninstall" button in the upper right corner of the application details page.

## Usage

After installing PostgreSQL cluster application, you could use it in all variations of workloads within the cluster, like jobs or other apps.

{% hint style="warning" %}
Currently, it is possible to connect to the database only from within the cluster workloads. Please, reach us if you need to expose the database to the outer world, we might help you find the solution.
{% endhint %}

While application interconnection in most of the cases is handled by the platform, you should know how to reach out database from your jobs.

In this small example, we'll use PostgreSQL container image as a client and connect to the database deployed previously.&#x20;

Step 1 — start a client job via CLI

```
apolo run postgres:16-bookworm -- psql '<Pgbouncer Uri>'
Using preset 'cpu-small'
Using image 'postgres:16-bookworm'
√ Job ID: job-d99bfd4b-97f2-466b-91a4-0106dfc3abc2
- Status: pending Creating
- Status: pending Scheduling
- Status: pending Pulling (Pulling image "postgres:16-bookworm")
- Status: pending ContainerCreating
√ Status: running
...
```

**Pgbouncer Uri** here is the output reported after the application being installed. Note, this output is only accessible to the participants of your project

Note: we advice selecting postgres image tag version matching the database version you deployed.

Step 2 — verify connection works, try creating minimal table

```
psql (16.9 (Debian 16.9-1.pgdg120+1), server 16.4)
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

√ ========= Job's output, may overlap with logs =========
dbone=> select 1;
 ?column? 
dbone=> CREATE TABLE sample_users (
dbone(>     id SERIAL PRIMARY KEY,
dbone(>     name VARCHAR(100),
dbone(>     email VARCHAR(100) UNIQUE,
dbone(>     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
dbone(> );
CREATE TABLE
dbone=> INSERT INTO sample_users (name, email) VALUES
dbone-> ('Alice', 'alice@example.com'),
dbone-> ('Bob', 'bob@example.com');
INSERT 0 2
dbone=> SELECT * FROM sample_users;
 id | name  |       email       |         created_at         
----+-------+-------------------+----------------------------
  1 | Alice | alice@example.com | 2025-05-27 09:33:07.994067
  2 | Bob   | bob@example.com   | 2025-05-27 09:33:07.994067
(2 rows)
```

Step 3 — terminate job

```
dbone=> exit
√ Job job-d99bfd4b-97f2-466b-91a4-0106dfc3abc2 finished successfully
```

And this is it!

Quick recap of what was done:

1. Deployed PostgreSQL cluster with backups, monitoring and configured RBAC
2. Verified configuration by connecting to the server from within Apolo Job and
   1. Creating sample table
   2. Inserting several records into the table
   3. Querying the table

## References

* [Crunchy Postgres Apolo Chart Repository](https://github.com/neuro-inc/app-crunchy-postgres)
* [Crunchy Data Postgres Documentation](https://access.crunchydata.com/documentation/postgres-operator/latest/quickstart)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
* [Apolo PostgreSQL application management via CLI](../../../apolo-concepts-cli/apps/available-apps/postgresql.md)
