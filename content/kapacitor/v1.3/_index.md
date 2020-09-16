---
title: Kapacitor 1.3 documentation

menu:
  kapacitor:
    name: v1.3
    identifier: kapacitor_1_3
    weight: 20
---

Kapacitor is an open source data processing framework that makes it easy to create
alerts, run ETL jobs and detect anomalies.
Kapacitor is the final piece of the [TICK stack](https://influxdata.com/time-series-platform/).

## Key features

Here are some of the features that Kapacitor currently supports that make it a
great choice for data processing.

* Process both streaming data and batch data.
* Query data from InfluxDB on a schedule, and receive data via the
[line protocol](/{{< latest "influxdb" >}}/write_protocols/) and any other method InfluxDB supports.
* Perform any transformation currently possible in [InfluxQL](/{{< latest "influxdb" >}}/query_language/spec/).
* Store transformed data back in InfluxDB.
* Add custom user defined functions to detect anomalies.
* Integrate with HipChat, OpsGenie, Alerta, Sensu, PagerDuty, Slack, and more.
