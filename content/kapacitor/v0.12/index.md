---
title: Kapacitor Version 0.12 Documentation

menu:
  kapacitor:
    name: v0.12
    identifier: kapacitor_012
    weight: 0
---

Kapacitor is the final piece of the [TICK stack](https://influxdata.com/time-series-platform/).
It's an open source data processing framework that makes it easy to create
alerts, run ETL jobs and detect anomalies.

## Key features

Here are some of the features that Kapacitor currently supports that make it a
great choice for data processing.

* Process both streaming data and batch data.
* Query data from InfluxDB on a schedule, and receive data via the
[line protocol](/influxdb/v0.12/write_protocols/line/) and any other method InfluxDB supports.
* Perform any transformation currently possible in [InfluxQL](/influxdb/v0.12/query_language/spec/).
* Store transformed data back in InfluxDB.
* Add custom user defined functions to detect anomalies.
* Integrate with HipChat, OpsGenie, Alerta, Sensu, PagerDuty, Slack, and more.
