---
title: Getting Started
menu:
  influxdb_08:
    identifier: getting_started
    weight: 20
    parent: introduction
---

Now that you've [installed InfluxDB](/influxdb/v0.8/introduction/installation/) you're ready to start doing awesome things.
There are many client libraries available for InfluxDB, but in this section we're going to use the built in user interface to get started quickly.

## Logging in and creating your first database
If you've installed locally, point your browser to <a href="http://localhost:8083" target="_blank">localhost:8083</a>.
The built in user interface runs on port `8083` by default.
You should see a screen like this:

![Admin login](/img/influxdb/old/admin_login.png)

The default options for hostname of `localhost` and port of `8086` should work.
The InfluxDB HTTP API runs on port `8086` by default.
Enter the username `root` and password `root` and click Connect.
You'll then see a logged in screen like this:

![Logged in with no databases](/img/influxdb/old/logged_in_no_databases.png)

Enter in a database name and click Create.
Database names should contain only letters, numbers, or underscores and start with a letter.
Once you've created a database you should see it on the screen:

![Database list screen](/img/influxdb/old/database_created.png)

## Writing and exploring data in the UI
Go ahead and click the "Explore" link to get here:

![Explore data interface](/img/influxdb/old/explore_screen.png)

From this screen you can write some test data.
More importantly, you'll be able to issue ad-hoc queries and see basic visualizations.
Let's write a little data in to see how things work.
Data in InfluxDB is organized by "time series" which then have "points" which have a `time`, `sequence_number`, and `columns`.
Think of it kind of like SQL tables, and rows where the primary index is always time.
The difference is that with InfluxDB you can have millions of series, you don't have to define schemas up front, and null values aren't stored.

Let's write some data.
Here are a couple of examples of things we'd want to write.
We'll show the screenshot and what the JSON data looks like right after.

![Storing log lines](/img/influxdb/old/log_lines.png)

If you're writing data in the web UI, you need to put something like this in the `Values` textbox:

```json
{
  "line": "here's some useful log info from paul@influxdb.com"
}
```

And now let's look at what the resulting JSON would look like if querying it after the write:

```json
[
  {
    "name" : "log_lines",
    "columns" : ["line"],
    "points" : [
      ["here's some useful log info from paul@influxdb.com"]
    ]
  }
]
```

![Storing response times](/img/influxdb/old/response_times.png)

Write values input:

```json
{
  "code": 200,
  "value": 234,
  "controller_action": "users#show"
}
```

Resulting JSON that will get returned on query:

```json
[
  {
    "name" : "response_times",
    "columns" : ["code", "value", "controller_action"],
    "points" : [
      [200, 234, "users#show"]
    ]
  }
]
```

![Storing user analytics data](/img/influxdb/old/user_events.png)

```json
[
  {
    "name" : "user_events",
    "columns" : ["type", "url_base", "user_id"],
    "points" : [
      ["add_friend", "friends#show", 23]
    ]
  }
]
```

![Storing sensor data](/img/influxdb/old/device_temperatures.png)

```json
[
  {
    "name" : "device_temperatures",
    "columns" : ["value"],
    "points" : [
      [88.2]
    ]
  }
]
```

Or a classic example from DevOps:

```json
[
  {
    "name" : "cpu_idle",
    "columns" : ["value", "host"],
    "points" : [
      [88.2, "serverA"]
    ]
  }
]
```

Now that we've written a few points.
Let's take a look at them.
Try some of the following queries:

```sql
select * from /.*/ limit 1
--
select * from cpu_idle
--
select * from response_times where value > 200
--
select * from user_events where url_base = 'friends#show'
--
select line from log_lines where line =~ /paul@influxdb.com/
```

![Selecting log lines on regex match](/img/influxdb/old/select_log_lines.png)

In the results of those queries we notice two columns that we didn't explicitly write in: `time` and `sequence_number`.
Those are automatically assigned by InfluxDB when you write data in if they're not specified.
In the UI time is represented as an epoch in seconds, but the underlying storage keeps them as microsecond epochs.

There's a lot more you can do with the query language.
Let's get to writing some test data to try things out.

## Writing Data Through JavaScript

Let's drop into the javascript console to write some test data.
That'll help us try some queries that show more of what InfluxDB can do.
While in the explore data interface, bring up the javascript console.
Copy and paste this code in and execute it.

```javascript
// start time of 24 hours ago
var backMilliseconds = 86000 * 1000;
var startTime = new Date() - backMilliseconds;
var timeInterval = 60 * 1000;
var eventTypes = ["click", "view", "post", "comment"];

var cpuSeries = {
  name:    "cpu_idle",
  columns: ["time", "value", "hostName"],
  points:  []
};

var eventSeries = {
  name:    "customer_events",
  columns: ["time", "customerId", "type"],
  points:  []
};

for (i = 0; i < backMilliseconds; i += timeInterval) {
  // generate fake cpu idle host values
  var hostName = "server" + Math.floor(Math.random() * 100);
  var value = Math.random() * 100;
  var pointValues = [startTime + i, value, hostName];
  cpuSeries.points.push(pointValues);

  // generate some fake customer events
  for (j = 0; j < Math.random() * 10; j += 1) {
    var customerId = Math.floor(Math.random() * 1000);
    var eventTypeIndex = Math.floor(Math.random() * 1000 % 4);
    var eventValues = [startTime + i, customerId, eventTypes[eventTypeIndex]];
    eventSeries.points.push(eventValues);
  }
}

influxdb.writeSeries([cpuSeries, eventSeries]);
```

`influxdb` is the javascript library that is available in that window.
Go [here for more info on using the InfluxDB javascript library](/influxdb/v0.8/client_libraries/javascript/).
But for now, let's run some queries:

Get the average of `cpu_idle` in 30 minute windows for the last day:

```sql
select mean(value) from cpu_idle
group by time(30m) where time > now() - 1d
```

![Cpu idle time in 30 minute windows](/img/influxdb/old/cpu_idle_mean_group_by.png)

Get the average of `cpu_idle` for `server1` in 30 minute windows for the last day:

```sql
select mean(value) from cpu_idle
group by time(30m)
where time > now() - 1d and hostName = 'server1'
```

![Cpu idle time for server1 in 30 minute windows](/img/influxdb/old/cpu_idle_mean_group_by_where_server.png)

Get the number of data points from the `cpu_idle` series for the last hour:

```sql
select count(value) from cpu_idle where time > now() - 1h
```

Get the number of `customer_events` in 10 minute windows for the last day:

```sql
select count(customerId) from customer_events
where time > now() - 1d group by time(10m)
```

![Customer events in 10 minute increments](/img/influxdb/old/customer_events_count_10m.png)

Find the unique customer ids from `customer_events` for the last hour:

```sql
select distinct(customerId) as customerId from customer_events
where time > now() - 1h
```

![Customer events in 10 minute increments](/img/influxdb/old/customer_events_distinct.png)

Count the number of `customer_events` per customer in 10 minute windows for the last 4 hours:

```sql
select count(customerId), customerId from customer_events
group by time(10m), customerId
where time > now() - 4h
```

The visualization for this one is off, but you can see the data returned in the table.

Now that you've gone through a quick intro to some of the things InfluxDB can do, you may want to pick up one of your favorite client libraries or take a look at the [InfluxDB query language guide](/influxdb/v0.8/api/query_language/).

Or take a look at building some [beautiful InfluxDB dashboards with Grafana](/influxdb/v0.8/ui/grafana/).
