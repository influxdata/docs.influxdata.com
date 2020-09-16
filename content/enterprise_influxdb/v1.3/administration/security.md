---
title: Security Best Practices
menu:
  enterprise_influxdb_1_3:
    weight: 90
    parent: Administration
---

Some customers may choose to install InfluxDB Enterprise with public internet access, however
doing so can inadvertently expose your data and invite unwelcome attacks on your database.
Check out the sections below for how protect the data in your InfluxEB Enterprise instance.

## Enable authentication

Password protect your InfluxDB Enterprise instance to keep any unauthorized individuals
from accessing your data.

Resources:
[Set up Authentication](https://docs.influxdata.com/influxdb/v1.3/query_language/authentication_and_authorization/#set-up-authentication)

## Manage users and their permissions

Restrict access by creating individual users and assigning them relevant
read and/or write permissions.

Resources:
[User Types and Privileges](https://docs.influxdata.com/influxdb/v1.3/query_language/authentication_and_authorization/#user-types-and-privileges),
[User Management Commands](https://docs.influxdata.com/influxdb/v1.3/query_language/authentication_and_authorization/#user-management-commands),
[Fine-Grained Authorization](/enterprise_influxdb/v1.3/guides/fine-grained-authorization/)

## Set up HTTPS

Using HTTPS secures the communication between clients and the InfluxDB server, and, in
some cases, HTTPS verifies the authenticity of the InfluxDB server to clients (bi-directional authentication).
The communicatio between the meta nodes and the data nodes are also secured via HTTPS.

Resources:
[HTTPS Setup](/enterprise_influxdb/v1.3/guides/https_setup/)

## Secure your host

### Ports
For InfluxDB Enterprise data nodes, close all ports on each host except for port `8086`.
You can also use a proxy to port `8086`.  By default, data nodes and meta nodes communicate with each other over '8088','8089',and'8091'

For InfluxDB Enterprise, [backups and restores](/enterprise_influxdb/v1.3/guides/backup-and-restore/) is performed from the meta nodes.


### AWS recommendations

We recommend implementing on-disk encryption; InfluxDB does not offer built-in support to encrypt the data.
