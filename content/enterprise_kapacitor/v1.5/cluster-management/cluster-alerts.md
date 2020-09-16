---
title: Configure alerts in a Kapacitor Enterprise cluster
description: Information about how to avoid duplicate alerts when sending alerts from a Kapacitor cluster.
menu:
  enterprise_kapacitor_1_5:
    name: Configure alerts in a cluster
    weight: 20
    parent: Cluster Management
---

## Terminology
Definitions for terms specific to this guide are defined below:

**Standalone event handler**  
"Standalone" event handlers (also known as "topic handlers") are those added using an
[event handler file](https://docs.influxdata.com/kapacitor/v1.5/event_handlers/#handler-file) that subscribes to a specified alert topic.
As alerts are published to that topic, the standalone event handler sends alert data to a given endpoint.
How the handler works and to what endpoint data is sent depends on the particular type of handler used
(defined by the `kind` attribute in your [handler file](#3-add-an-alert-handler-that-subscribes-to-your-topic)).

**Inline event handler**  
"Inline" event handlers are those [used in TICKscripts](https://docs.influxdata.com/kapacitor/v1.5/event_handlers/#tickscript)
that send alert data directly from the running task.

## Cluster awareness
Currently, Kapacitor Enterprise is only partially cluster-aware.
For example, members in a Kapacitor cluster aren't aware of tasks and TICKscripts
running on other members in the cluster.
Tasks need to be defined on each member on which you wish the task to run.
However, "standalone" alert handlers only need to be defined on one member.
Kapacitor will manage the replication of that handler across other members of the cluster.

## Alert handlers in a cluster
Because members of a Kapacitor Enterprise cluster are not aware of tasks running on
other members, alert data should **not** be sent directly from TICKscripts.
Doing so will result in duplicate notifications as each member running the same task
will send the same alert.

{{< inline-svg svg="static/img/svgs/kapacitor-cluster-alerts-duplicates.svg" >}}

Instead, publish alert data to a topic and create a standalone alert handler that subscribes to the topic.
Kapacitor Enterprise handles the replication of standalone handlers across the cluster.
As multiple members publish alerts a topic, Kapacitor **deduplicates** the alerts so the
standalone handler will not send duplicate alerts.

{{< inline-svg svg="static/img/svgs/kapacitor-cluster-alerts-topic.svg" >}}

## Add alerts to a Kapacitor cluster
To add alerts to your Kapacitor Enterprise cluster without worrying about duplicate alerts,
do the following:

### 1. Publish alert data to a topic
In your TICKscript [AlertNodes](https://docs.influxdata.com/kapacitor/v1.5/nodes/alert_node/), publish alert
data to a topic using the `.topic()` method.

_**TICKscript - Publish alerts to a topic**_
```js
|alert()
  // ...
  .topic('topic-name')
```

### 2. Add the task to nodes in your cluster
Since tasks are not replicated across members in you cluster, tasks need to be defined on each member.
Login to each member you wish to run a given task and define a new task using your TICKscript:

```bash
# Pattern
kapacitor define <task-name> -tick <path-to-tickscript-file>

# Example
kapacitor define cpu-alert -tick cpu_alert.tick
```

> InfluxData recommends defining a given task on the number members set in the
> `redundancy` configuration option of the `[alert]` section of your `kapacitor.conf`.
> For example, if you set `redundancy` to 2, then each task that generates alerts
> should be defined on 2 members of the cluster.

### 3. Add an alert handler that subscribes to your topic
Standalone alert handlers are defined using [handler files](https://docs.influxdata.com/kapacitor/v1.5/event_handlers/#handler-file),
simple YAML files that includes alert handler information.
Below is an example [Slack](https://slack.com) handler file that subscribes to the `topic-name` topic:

_**slack-handler.yml**_
```yaml
id: handler-id
topic: topic-name
kind: slack
options:
  workspace: 'workspace.slack.com'
  channel: '#alerts'
  username: 'kapacitor'
```

Standalone handlers only need to be defined on one member of your cluster.
Kapacitor Enterprise manages the replication of standalone handlers across the cluster.
Login to one of the members in your cluster and define your alert handler:

```bash
# Pattern
kapacitor define-topic-handler <path/to/handler.yml>

# From inside a running Kapacitor instance
kapacitor define-topic-handler ./slack-handler.yml
```
