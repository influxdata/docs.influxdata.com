---
title: InfluxDB OSS Stats monitoring dashboard
description: Describes metrics monitored in the InfluxDB OSS monitoring dashboard.
menu:
  platform:
    name: InfluxDB OSS Stats dashboard
    parent: Dashboards for monitoring
    weight: 40
---

The InfluxDB OSS Stats dashboard is useful for monitoring the health of your InfluxDB OSS instances. The dashboard visualizations cover commonly monitored metrics that are important for monitoring and maintaining your InfluxDB OSS instance and for troubleshooting. So that you don't have to constantly check the dashboard, you can create alerts to notify you when attention is required.

> **Note:** The queries below use the `_internal` database, which is enabled by default on InfluxDB OSS instances.

## Continuous Queries Executed / Minute

### Description

Displays the non-negative mean rate of change in continuous queries (CQs) executed per minute.

### Query

`SELECT non_negative_derivative(mean(/.*/),60s) FROM "_internal".."cq" WHERE time > :dashboardTime: GROUP BY hostname, time(:interval:) fill(null)"`

### Metrics

#### Continuous Queries

`non_negative_derivative(mean(/.*/),60s)`

### Measurement

[`cq`](/platform/monitoring/tools/measurements-internal#cq)

### Field keys

[`queryOk`](/platform/monitoring/tools/measurements-internal#queryok), [`queryFail`](/platform/monitoring/tools/measurements-internal#queryfail)

_______________

## Heap Size

### Description

Displays the current heap size.

### Queries

`"SELECT mean("HeapInUse") FROM "_internal".."runtime" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname" fill(null)"`

### Metrics

#### Heap Size

`mean("HeapInUse")`

## Measurement

[`runtime`](/platform/monitoring/tools/measurements-internal#runtime)

### Field keys

[`HeapInUse`](/platform/monitoring/tools/measurements-internal#heapinuse)

_________

## Shard Write Errors

### Description

Displays the number of shard write errors.

### Query

`"SELECT non_negative_derivative(max("writeError"), 10s) FROM "_internal".."write" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname" fill(null)"`

### Metrics

#### [Shard Write Errors]

`non_negative_derivative(max("writeError")`

### Measurement

[`write`](/platform/monitoring/tools/measurements-internal#write)

### Field keys

[`writeError`](/platform/monitoring/tools/measurements-internal#writeerror)

___________

## Series Cardinality by Database

### Description

Displays the number of series (series cardinality) for the specified databases.

### Queries

`"SELECT max("numSeries") AS "Series Cardinality" FROM "_internal".."database" WHERE time > :dashboardTime:  GROUP BY time(:interval:), "database" fill(null)"`

### Metrics

#### Series Cardinality

`max("numSeries")`

### Measurement

[`database`](/platform/monitoring/tools/measurements-internal#database)

### Field keys

[`numSeries`](/platform/monitoring/tools/measurements-internal#numseries)

_____

## Number of Measurements by Database

### Description

Displays the number of measurements, by database.

### Query

`"SELECT max("numMeasurements") AS "Measurements" FROM "_internal".."database" WHERE time > :dashboardTime: GROUP BY time(:interval:), "database" fill(null)"`

### Metric

#### Number of measurements

`max("numMeasurements")`

### Measurement

[`database`](/platform/monitoring/tools/measurements-internal#database)

### Field keys

[`numMeasurements`](/platform/monitoring/tools/measurements-internal#nummeasurements)

_____

## HTTP Request Duration (99th %)

### Description

Displays the duration, in nanoseconds, of the top 1% of HTTP requests.

### Queries

#### Write Request

`"SELECT non_negative_derivative(percentile("writeReqDurationNs", 99)) /  non_negative_derivative(max("writeReq")) AS "Write Request" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY hostname, time(:interval:) fill(0)\t"`

#### Query Request

`"SELECT non_negative_derivative(percentile("queryReqDurationNs", 99)) / non_negative_derivative(max("queryReq")) AS "Query Request" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY hostname, time(:interval:)"`

### Metrics

#### Write Request

`"non_negative_derivative(percentile("writeReqDurationNs", 99)) /  non_negative_derivative(max("writeReq"))`

#### Read Request

`non_negative_derivative(percentile("queryReqDurationNs", 99)) / non_negative_derivative(max("queryReq"))`

### Measurement

[`httpd`](/platform/monitoring/tools/measurements-internal#httpd)

### Field keys

[`queryReq`](/platform/monitoring/tools/measurements-internal#queryreq), [`queryReqDurationNs`](/platform/monitoring/tools/measurements-internal#queryreqdurationns). [`writeReq`](/platform/monitoring/tools/measurements-internal#writereq), [`writeReqDurationNs`](/platform/monitoring/tools/measurements-internal#writereqdurationns)

____

## Point Throughput / Minute by Hostname

### Description

Displays the number of points requested each minute, by hostname.

### Queries

`"SELECT non_negative_derivative(max("pointReq"), 60s) FROM "_internal".."write" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname""`

### Metrics

#### Point Requests

 `non_negative_derivative(max("pointReq"), 60s)`

### Measurement

 [`write`](/platform/monitoring/tools/measurements-internal#write)

### Field keys

[`pointReq`](/platform/monitoring/tools/measurements-internal#pointreq)

_____

## Queries Executed / Minute

### Description

Displays the number of queries executed per minute.

### Queries

#### Queries Executed

 `"SELECT non_negative_derivative(mean("queriesExecuted"), 60s) AS "Queries Executed" FROM "_internal".."queryExecutor" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname" fill(null)"`

### Metrics

#### Queries Executed

`non_negative_derivative(mean("queriesExecuted"), 60s)`

### Measurement

[`queryExecutor`](/platform/monitoring/tools/measurements-internal#queryexecutor)

### Field keys

[`queriesExecuted`](/platform/monitoring/tools/measurements-internal#queriesexecuted)

_____

## HTTP Requests / Minute

### Description

Displays the number of HTTP requests per minute.

### Queries

#### Query Requests

`"SELECT non_negative_derivative(mean("queryReq"), 60s) AS "Query" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname""`

#### Write Requests

`"SELECT non_negative_derivative(mean("writeReq"), 60s) AS "Write Requests" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname""`

#### Server Errors

`"SELECT non_negative_derivative(mean("serverError"), 60s) AS "Server Errors" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname""`

#### Client Errors

`"SELECT non_negative_derivative(mean("clientError"), 60s) AS "Client Errors" FROM "_internal".."httpd" WHERE time > :dashboardTime: GROUP BY time(:interval:), "hostname""`

### Metrics

#### Query Requests

`non_negative_derivative(mean("queryReq"), 60s)`

#### Write Requests

`non_negative_derivative(mean("writeReq"), 60s)`

#### Server Errors

`non_negative_derivative(mean("serverError"), 60s)`

#### Client Errors

`non_negative_derivative(mean("clientError"), 60s)`

### Measurement

[`httpd`](/platform/monitoring/tools/measurements-internal#httpd)

### Field keys

[`queryReq`](/platform/monitoring/tools/measurements-internal#queryreq), [`writeReq`](/platform/monitoring/tools/measurements-internal#writereq), [`serverError`](/platform/monitoring/tools/measurements-internal#servererror), [`clientError`](/platform/monitoring/tools/measurements-internal#clienterror)
