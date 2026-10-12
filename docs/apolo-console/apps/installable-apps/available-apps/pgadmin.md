# pgAdmin

## Overview

[pgAdmin](https://www.pgadmin.org/) is the web interface for PostgreSQL. The app opens the [PostgreSQL](postgre-sql/) apps of your project in pgAdmin, so you can query them from the browser without copying passwords or forwarding ports.

With it you can:

* Run SQL in the Query Tool and export the results to CSV
* Browse schemas, tables, views and their data
* Look at sessions, locks and statistics of a database

## Installing the pgAdmin App

1. Open the **Apps** section of the **Apolo Console**
2. Find **pgAdmin** in the list of available apps
3. Click **Install**

### 1. Resource Preset

Choose a preset with at least 512 MB of memory.

### 2. Networking

pgAdmin has no login of its own and connects to the databases with the stored credentials, so the ingress must require authentication: keep **Apolo Platform Authentication** or use a custom authentication middleware. **No Authentication** is rejected.

### 3. Databases

Add one entry per database:

* **PostgreSQL Credentials** – pick a user of an installed [PostgreSQL](postgre-sql/) app through **App Integration**. The user's database is the one pgAdmin opens
* **Display Name** – the name of the server in pgAdmin, the database name by default
* **Allow Writes** – off by default: sessions are read-only, see [Read-Only Sessions](#read-only-sessions)

### 4. Install

Click **Install**. When the app is healthy, open it from the app outputs.

## Using pgAdmin

The databases are listed in the **Databases** group of the **Object Explorer**. Expand one to connect: no password is asked.

To run a query, select the database in the tree and open **Tools → Query Tool**, write the SQL and click **Execute**. The results appear under the editor; the download button saves them as CSV.

To add or remove a database, open the installed app, change **Databases** and save. pgAdmin restarts with the new list.

## Read-Only Sessions

Unless **Allow Writes** is on, every session to the database starts with `default_transaction_read_only = on`, and a write fails with `cannot execute INSERT in a read-only transaction`.

This prevents changing data by mistake. It is not a permission: a session can turn it off with `SET default_transaction_read_only = off`. To make writes impossible, give pgAdmin a PostgreSQL user that has only read privileges.

## Limits

* pgAdmin keeps its own state, such as the query history and preferences, until the app restarts. A restart, including one after a configuration change, starts from a clean state
* Everyone who can open the app URL uses the same pgAdmin session settings and the same database credentials
* A database of another project is not reachable

## References

* [pgAdmin documentation](https://www.pgadmin.org/docs/pgadmin4/latest/index.html)
* [Query Tool](https://www.pgadmin.org/docs/pgadmin4/latest/query_tool.html)
