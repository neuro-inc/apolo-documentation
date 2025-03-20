# PostgreSQL

PostgreSQL is a powerful, open-source relational database system known for its reliability, feature richness, and extensibility. It supports advanced SQL compliance, transactional integrity, and scalability, making it a popular choice for both OLTP and OLAP workloads. PostgreSQL’s flexibility allows for use across a wide range of applications, from web services to large-scale data platforms.Apolo uses the **Crunchy Data distribution of PostgreSQL**, which builds on the core database engine by adding production-grade features such as high availability, automated backups, Kubernetes-native deployment, and enhanced monitoring. This ensures that PostgreSQL on Apolo is robust, secure, and ready for mission-critical workloads in modern cloud environments.

#### Key Features <a href="#key-features" id="key-features"></a>

* **Enterprise-Grade PostgreSQL**: Fully open-source and compliant with PostgreSQL, offering advanced tooling and support for critical workloads.
* **High Availability**: Built-in support for HA via Patroni, Kubernetes-native failover handling, and synchronous streaming replication.
* **Automated Backups & Point-in-Time Recovery (PITR)**: Integrated tools for automated scheduled backups, WAL archiving, and recovery to any point.
* **Monitoring & Metrics**: Native integration with Prometheus and Grafana for visibility into database performance and health.
* **Security & Compliance**: Includes features such as TLS encryption, role-based access control, audit logging, and SELinux hardening.
* **Kubernetes-Native**: Optimized for Kubernetes through Crunchy Data’s PostgreSQL Operator (PGO), enabling declarative management and scalable deployments.

#### Installation and Deployment on Apolo <a href="#installation-and-deployment-on-apolo" id="installation-and-deployment-on-apolo"></a>

You can deploy Crunchy Postgres using an App interface

**Highlights of the Apolo Installation Flow:**

* **Preset-Based Deployment**: Select a suitable Apolo preset (e.g. `cpu-medium`, `cpu-large`) that defines CPU, memory, and storage requirements.
* **Persistent Storage Management**: Automatically provisions high-performance, durable storage with snapshot and backup support.
* **Secrets & Credentials Injection**: Securely injects environment variables and secrets such as `POSTGRES_PASSWORD`, `PGDATA`, and SSL keys.
* **Ingress Configuration**: Easily expose your PostgreSQL instance using an ingress or internal service, depending on access needs.
* **Backup & PITR Support**: Integrates with Apolo’s backup system for regular snapshots and point-in-time recovery options.
* **Observability**: Exposes key PostgreSQL metrics via built-in exporters for monitoring via Apolo’s observability stack or your own Prometheus instance.

***

#### Parameter Descriptions:

The following parameters can be set with Apolo’s CLI&#x20;

(`apolo run --pass-config ... install ... --set <key>=<value>`).

Mainly parameters for the Instances and PgBouncer.&#x20;

**PgBouncer** sits between your application and PostgreSQL, managing and reusing database connections to reduce overhead and improve performance—especially in high-concurrency environments.

| Parameter                   | Type   | Description                                                                                                          |
| --------------------------- | ------ | -------------------------------------------------------------------------------------------------------------------- |
| **instanceReplicas**        | Int    | Required. Number of PostgreSQL instances deployed.                                                                   |
| **pgBouncerReplicas**       | Int    | Required. Number of PgBouncer instances deployed.                                                                    |
| **preset\_name**            | String | Required. Apolo preset for resources. E.g. `cpu-large`,`cpu-medium` . Sets CPU, memory, GPU count, and GPU provider. |
| **bouncer\_preset\_name**   | String | Required. Apolo preset for pgBouncer. E.g. `cpu-large`,`cpu-medium` . Sets CPU, memory, GPU count, and GPU provider. |
| **users\[N].name**          | String | Optional. Username to access the database later on.                                                                  |
| **users\[N].databases\[N]** | String | Optional. Database created and given access to specific user.                                                        |

Any additional chart values can also be provided through `--set` flags, but the above are the most common.

#### Example Apolo CLI Command

Below is a streamlined example command that deploys **Postgres** using the [`postgres-app`](https://github.com/neuro-inc/app-llm-inference)

```
  apolo run \
  --pass-config \
  --entrypoint "./entrypoints/pgo.sh install postgresql pgv \
  --set 'instanceReplicas=2' --set 'pgBouncerReplicas=2' \
  --set 'preset_name=cpu-large' \
  --set 'bouncer_preset_name=cpu-large' \
  --set 'users[0].name=myuser' --set 'users[0].databases[0]=mydb'" \
  ghcr.io/neuro-inc/app-deployment
```

### References:

* [Crunchy Postgres Apolo Chart Repository](https://github.com/neuro-inc/app-crunchy-postgres)
* [Crunchy Data Postgres Documentation](https://access.crunchydata.com/documentation/postgres-operator/latest/quickstart)
* [Apolo Documentation](https://docs.apolo.us/apolo-cli/commands/shortcuts#usage-16) (for the usage of `apolo run` and resource presets)
