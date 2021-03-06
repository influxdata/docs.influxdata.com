---
title: Managing InfluxDB users in Chronograf
description: Using Chronograf to enable authentication and manage InfluxDB OSS and InfluxDB Enterprise users.
aliases:
  - /(/chronograf/v1.5/administration/managing-influxdb-users/
menu:
  chronograf_1_5:
    name: Managing InfluxDB users
    weight: 60
    parent: Administration
---

The **Chronograf Admin** provides InfluxDB user management for InfluxDB OSS and InfluxDB Enterprise users.


> ***Note:*** For details on Chronograf user authentication and management, see [Managing security](/chronograf/v1.5/administration/managing-security/).

**On this page:**

* [Enabling authentication](#enabling-authentication)
* [InfluxDB OSS user management](#influxdb-oss-user-management)
* [InfluxDB Enterprise user management](#influxdb-enterprise-user-management)

## Enabling authentication

Follow the steps below to enable authentication.
The steps are the same for InfluxDB OSS instances and InfluxEnterprise clusters.

> ***InfluxEnterprise clusters:***
> Repeat the first three steps for each data node in a cluster.

### Step 1: Enable authentication.

Enable authentication in the InfluxDB configuration file.
For most Linux installations, the configuration file is located in `/etc/influxdb/influxdb.conf`.

In the `[http]` section of the InfluxDB configuration file (`influxdb.conf`), uncomment the `auth-enabled` option and set it to `true`, as shown here:

```
[http]
  # Determines whether HTTP endpoint is enabled.
  # enabled = true

  # The bind address used by the HTTP service.
  # bind-address = ":8086"

  # Determines whether HTTP authentication is enabled.
  auth-enabled = true #
```

### Step 2: Restart the InfluxDB service.

Restart the InfluxDB service for your configuration changes to take effect:

```
~# sudo systemctl restart influxdb
```

### Step 3: Create an admin user.

Because authentication is enabled, you need to create an [admin user](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-types-and-privileges) before you can do anything else in the database.
Run the `curl` command below to create an admin user, replacing:

* `localhost` with the IP or hostname of your InfluxDB OSS instance or one of your InfluxEnterprise data nodes
* `chronothan` with your own username
* `supersecret` with your own password (note that the password requires single quotes)

```
~# curl -XPOST "http://localhost:8086/query" --data-urlencode "q=CREATE USER chronothan WITH PASSWORD 'supersecret' WITH ALL PRIVILEGES"
```

A successful `CREATE USER` query returns a blank result:

```
{"results":[{"statement_id":0}]}   <--- Success!
```

### Step 4: Edit the InfluxDB source in Chronograf.

If you've already [connected your database to Chronograf](/chronograf/v1.5/introduction/getting-started/#3-connect-to-chronograf), update the connection configuration in Chronograf with your new username and password.
Edit existing InfluxDB database sources by navigating to the Chronograf configuration page and clicking on the name of the source.

## InfluxDB OSS User Management

On the **Chronograf Admin** page:

* View, create, and delete admin and non-admin users
* Change user passwords
* Assign admin and remove admin permissions to or from a user

![InfluxDB OSS user management](/img/chronograf/chrono-admin-usermanagement-oss.png)

InfluxDB users are either admin users or non-admin users.
See InfluxDB's [authentication and authorization](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-types-and-privileges) documentation for more information about those user types.

> ***Note:*** Note that Chronograf currently does not support assigning InfluxDB database `READ`or `WRITE` access to non-admin users.
>This is a known issue.
>
>As a workaround, grant `READ`, `WRITE`, or `ALL` (`READ` and `WRITE`) permissions to non-admin users with the following curl commands, replacing anything inside `< >` with your own values:

#### Grant `READ` permission:
```
~# curl -XPOST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" --data-urlencode "q=GRANT READ ON <database-name> TO <non-admin-username>"
```

#### Grant `WRITE` permission:
```
~# curl -XPOST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" --data-urlencode "q=GRANT WRITE ON <database-name> TO <non-admin-username>"
```

#### Grant `ALL` permission:
```
~# curl -XPOST "http://<InfluxDB-IP>:8086/query?u=<username>&p=<password>" --data-urlencode "q=GRANT ALL ON <database-name> TO <non-admin-username>"
```

In all cases, a successful `GRANT` query returns a blank result:
```
{"results":[{"statement_id":0}]}   <--- Success!
```
Remove `READ`, `WRITE`, or `ALL` permissions from non-admin users by replacing `GRANT` with `REVOKE` in the curl commands above.

## InfluxDB Enterprise user management

On the `Admin` page:

* View, create, and delete users
* Change user passwords
* Assign and remove permissions to or from a user
* Create, edit, and delete roles
* Assign and remove roles to or from a user

![InfluxDB Enterprise user management](/img/chronograf/chrono-admin-usermanagement-cluster.png)

### User types

Admin users have the following permissions by default:

* [CreateDatabase](#createdatabase)
* [CreateUserAndRole](#createuserandrole)
* [DropData](#dropdata)
* [DropDatabase](#dropdatabase)
* [ManageContinuousQuery](#managecontinuousquery)
* [ManageQuery](#managequery)
* [ManageShard](#manageshard)
* [ManageSubscription](#managesubscription)
* [Monitor](#monitor)
* [ReadData](#readdata)
* [WriteData](#writedata)

Non-admin users have no permissions by default.
Assign permissions and roles to both admin and non-admin users.

### Permissions

#### AddRemoveNode
Permission to add or remove nodes from a cluster.

**Relevant `influxd-ctl` arguments**:
[`add-data`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#add-data),
[`add-meta`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#add-meta),
[`join`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#join),
[`remove-data`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#remove-data),
[`remove-meta`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#remove-meta), and
[`leave`](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#leave)

**Pages in Chronograf that require this permission**: NA

#### CopyShard
Permission to copy shards.

**Relevant `influxd-ctl` arguments**:
[copy-shard](/{{< latest "enterprise_influxdb" >}}/features/cluster-commands/#copy-shard)

**Pages in Chronograf that require this permission**: NA

#### CreateDatabase
Permission to create databases, create [retention policies](/{{< latest "influxdb" >}}/concepts/glossary/#retention-policy-rp), alter retention policies, and view retention policies.

**Relevant InfluxQL queries**:
[`CREATE DATABASE`](/{{< latest "influxdb" >}}/query_language/database_management/#create-database),
[`CREATE RETENTION POLICY`](/{{< latest "influxdb" >}}/query_language/database_management/#create-retention-policies-with-create-retention-policy),
[`ALTER RETENTION POLICY`](/{{< latest "influxdb" >}}/query_language/database_management/#modify-retention-policies-with-alter-retention-policy), and
[`SHOW RETENTION POLICIES`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-retention-policies)

**Pages in Chronograf that require this permission**: Dashboards, Data Explorer, and Databases on the Admin page

#### CreateUserAndRole
Permission to manage users and roles; create users, drop users, grant admin status to users, grant permissions to users, revoke admin status from users, revoke permissions from users, change user passwords, view user permissions, and view users and their admin status.

**Relevant InfluxQL queries**:
[`CREATE USER`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-management-commands),
[`DROP USER`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#general-admin-and-non-admin-user-management),
[`GRANT ALL PRIVILEGES`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-management-commands),
[`GRANT [READ,WRITE,ALL]`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#non-admin-user-management),
[`REVOKE ALL PRIVILEGES`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-management-commands),
[`REVOKE [READ,WRITE,ALL]`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#non-admin-user-management),
[`SET PASSWORD`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#general-admin-and-non-admin-user-management),
[`SHOW GRANTS`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#non-admin-user-management), and
[`SHOW USERS`](/{{< latest "influxdb" >}}/query_language/authentication_and_authorization/#user-management-commands)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards, Users and Roles on the Admin page

#### DropData
Permission to drop data, in particular [series](/{{< latest "influxdb" >}}/concepts/glossary/#series) and [measurements](/{{< latest "influxdb" >}}/concepts/glossary/#measurement).

**Relevant InfluxQL queries**:
[`DROP SERIES`](/{{< latest "influxdb" >}}/query_language/database_management/#drop-series-from-the-index-with-drop-series),
[`DELETE`](/{{< latest "influxdb" >}}/query_language/database_management/#delete-series-with-delete), and
[`DROP MEASUREMENT`](/{{< latest "influxdb" >}}/query_language/database_management/#delete-measurements-with-drop-measurement)

**Pages in Chronograf that require this permission**: NA

#### DropDatabase
Permission to drop databases and retention policies.

**Relevant InfluxQL queries**:
[`DROP DATABASE`](/{{< latest "influxdb" >}}/query_language/database_management/#delete-a-database-with-drop-database) and
[`DROP RETENTION POLICY`](/{{< latest "influxdb" >}}/query_language/database_management/#delete-retention-policies-with-drop-retention-policy)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards, Databases on the Admin page

#### KapacitorAPI
Permission to access the API for InfluxKapacitor Enterprise.
This does not include configuration-related API calls.

**Pages in Chronograf that require this permission**: NA

#### KapacitorConfigAPI
Permission to access the configuration-related API calls for InfluxKapacitor Enterprise.

**Pages in Chronograf that require this permission**: NA

#### ManageContinuousQuery
Permission to create, drop, and view [continuous queries](/{{< latest "influxdb" >}}/concepts/glossary/#continuous-query-cq).

**Relevant InfluxQL queries**:
[`CreateContinuousQueryStatement`](/{{< latest "influxdb" >}}/query_language/continuous_queries/),
[`DropContinuousQueryStatement`](), and
[`ShowContinuousQueriesStatement`](/{{< latest "influxdb" >}}/query_language/continuous_queries/#list-cqs)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards

#### ManageQuery
Permission to view and kill queries.

**Relevant InfluxQL queries**:
[`SHOW QUERIES`](/{{< latest "influxdb" >}}/troubleshooting/query_management/#list-currently-running-queries-with-show-queries) and
[`KILL QUERY`](/{{< latest "influxdb" >}}/troubleshooting/query_management/#stop-currently-running-queries-with-kill-query)

**Pages in Chronograf that require this permission**: Queries on the Admin page

#### ManageShard
Permission to copy, delete, and view [shards](/{{< latest "influxdb" >}}/concepts/glossary/#shard).

**Relevant InfluxQL queries**:
[`DropShardStatement`](/{{< latest "influxdb" >}}/query_language/database_management/#delete-a-shard-with-drop-shard),
[`ShowShardGroupsStatement`](/{{< latest "influxdb" >}}/query_language/spec/#show-shard-groups), and
[`ShowShardsStatement`](/{{< latest "influxdb" >}}/query_language/spec/#show-shards)

**Pages in Chronograf that require this permission**: NA

#### ManageSubscription
Permission to create, drop, and view [subscriptions](/{{< latest "influxdb" >}}/concepts/glossary/#subscription).

**Relevant InfluxQL queries**:
[`CREATE SUBSCRIPTION`](/{{< latest "influxdb" >}}/query_language/spec/#create-subscription),
[`DROP SUBSCRIPTION`](/{{< latest "influxdb" >}}/query_language/spec/#drop-subscription), and
[`SHOW SUBSCRIPTIONS`](/{{< latest "influxdb" >}}/query_language/spec/#show-subscriptions)

**Pages in Chronograf that require this permission**: Alerting

#### Monitor
Permission to view cluster statistics and diagnostics.

**Relevant InfluxQL queries**:
[`SHOW DIAGNOSTICS`](/{{< latest "influxdb" >}}/troubleshooting/statistics/) and
[`SHOW STATS`](/{{< latest "influxdb" >}}/troubleshooting/statistics/)

**Pages in Chronograf that require this permission**: Data Explorer, Dashboards

#### ReadData
Permission to read data.

**Relevant InfluxQL queries**:
[`SHOW FIELD KEYS`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-field-keys),
[`SHOW MEASUREMENTS`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-measurements),
[`SHOW SERIES`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-series),
[`SHOW TAG KEYS`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-tag-keys),
[`SHOW TAG VALUES`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-tag-values), and
[`SHOW RETENTION POLICIES`](/{{< latest "influxdb" >}}/query_language/schema_exploration/#show-retention-policies)

**Pages in Chronograf that require this permission**: Admin, Alerting, Dashboards, Data Explorer, Host List

#### WriteData
Permission to write data.

**Relevant InfluxQL queries**: NA

**Pages in Chronograf that require this permission**: NA

### Roles
Roles are groups of permissions.
Assign roles to one or more users.

For example, the image below contains three roles: `CREATOR`, `DESTROYER`, and `POWERLESS`.
`CREATOR` includes two permissions (`CreateDatbase` and `CreateUserAndRole`) and is assigned to one user (`chrononut`).
`DESTROYER` also includes two permissions (`DropDatabase` and `DropData`) and is assigned to two users (`chrononut` and `chronelda`).

![InfluxDB OSS user management](/img/chronograf/chrono-admin-usermanagement-roles.png)
