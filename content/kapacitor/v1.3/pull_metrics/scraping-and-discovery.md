---
title: Scraping and Discovery

menu:
  kapacitor_1_3:
    weight: 0
    parent: pull_metrics
---

Pull data from a dynamic list of remote targets with Kapacitor's discovery and scraping features.
Use those features with Kapacitor's [TICKscripts](/kapacitor/v1.3/tick/) to monitor targets, process the data, and write data to [InfluxDB](/influxdb/v1.2/).
Currently, Kapacitor supports only Prometheus style targets.

>**Note**: Scraping and discovery is currently under technical preview.
There may be changes to the configuration and behavior in subsequent releases.

### Content

* [Overview](#overview)
* [Configuring Scrapers and Discoverers](#configuring-scrapers-and-discoverers)

## Overview

![image](/img/kapacitor/pull-metrics.png)

The diagram above outlines the infrastructure for discovering and scraping data with Kapacitor.

First, Kapacitor implements the discovery process to identify the available targets in your infrastructure.
It requests that information at regular intervals and receives that information from an [authority](#available-discoverers).
In the diagram, the authority informs Kapacitor of three targets: `A`, `B`, and `C`.

Next, Kapacitor implements the scraping process to pull metrics data from the existing targets.
It runs the scraping process at regular intervals.
Here, Kapacitor requests metrics from targets `A`, `B`, and `C`.
The application running on `A`, `B`, and `C` exposes a `/metrics` endpoint on its HTTP API which returns application-specific statistics.

Finally, Kapacitor processes the data according to your configured [TICKscripts](/kapacitor/v1.3/tick/).
Use TICKscripts to filter, transform, and perform other tasks on your metrics data.
In addition, if you'd like to store your data, configure a TICKscript to send them to [InfluxDB](/influxdb/v1.2/).

### Pushing vs. Pulling Metrics

By combining discovery with scraping, Kapacitor enables your metrics gathering infrastructure to pull metrics off of targets instead of requiring them to push metrics out to InfluxDB.
Pulling metrics has several advantages in dynamic environments where a target may have a short lifecycle.

## Configuring Scrapers and Discoverers

A single scraper scrapes the targets from a single discoverer.
Configuring both scrapers and discoverers comes down to configuring each individually and then informing the scraper about the discoverer.

Below are all the configuration options for a scraper.

```
[[scraper]]
  enabled = false
  name = "myscraper"
  # ID of the discoverer to use
  discoverer-id = ""
  # The kind of discoverer to use
  discoverer-service = ""
  db = "mydb"
  rp = "myrp"
  type = "prometheus"
  scheme = "http"
  metrics-path = "/metrics"
  scrape-interval = "1m0s"
  scrape-timeout = "10s"
  username = ""
  password = ""
  bearer-token = ""
  ssl-ca = ""
  ssl-cert = ""
  ssl-key = ""
  ssl-server-name = ""
  insecure-skip-verify = false
```


### Available Discoverers

Kapacitor supports the following service for discovery:

| Name             | Description                                                                                                               |
| ----             | -----------                                                                                                               |
| azure            | Discover targets hosted in [Azure](https://azure.microsoft.com/).                                                         |
| consul           | Discover targets using [Consul](https://www.consul.io/) service discovery.                                                |
| dns              | Discover targets via DNS queries.                                                                                         |
| ec2              | Discover targets hosted in [AWS EC2](https://aws.amazon.com/ec2/).                                                        |
| file-discovery   | Discover targets listed in files.                                                                                         |
| gce              | Discover targets hosted in [GCE](https://cloud.google.com/compute/).                                                      |
| kubernetes       | Discover targets hosted in [Kubernetes](https://kubernetes.io/).                                                          |
| marathon         | Discover targets using [Marathon](https://mesosphere.github.io/marathon/) service discovery.                              |
| nerve            | Discover targets using [Nerve](https://github.com/airbnb/nerve) service discovery.                                        |
| serverset        | Discover targets using [Serversets](https://github.com/twitter/finagle/tree/master/finagle-serversets) service discovery. |
| static-discovery | Statically list targets.                                                                                                  |
| triton           | Discover targets using [Triton](https://github.com/joyent/triton) service discovery.                                      |


See the example [configuration file](https://github.com/influxdata/kapacitor/blob/master/etc/kapacitor/kapacitor.conf) for details on configuring each discoverer.
