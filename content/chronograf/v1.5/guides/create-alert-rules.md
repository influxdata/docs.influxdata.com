---
title: Creating Chronograf alert rules
description: Creating Chronograf alert rules, specifying time series data and thresholds. Example sends alerts to a Slack channel.
aliases:
  - /chronograf/v1.5/guides/create-a-kapacitor-alert/
menu:
  chronograf_1_5:
    name: Creating alert rules
    weight: 60
    parent: Guides
---


Chronograf provides a user interface for [Kapacitor](/{{< latest "kapacitor" >}}/), InfluxData's processing framework for creating alerts, running ETL jobs, and detecting anomalies in your data.
Chronograf alert rules correspond to Kapacitor tasks that are designed specifically to
trigger alerts whenever data stream values rise above or fall below
designated thresholds.
Common alerting use cases that can be managed using Chronograf include:

* Thresholds with static ceilings, floors, and ranges.
* Relative thresholds based on unit or percentage changes.
* Deadman switches.

Complex alerts and other tasks must be defined directly in Kapacitor, but can be used within Chronograf.

Follow this guide to create a Chronograf alert rule that sends an alert message to an existing [Slack](https://slack.com/) channel whenever your idle CPU usage crosses the 80% threshold.

## Requirements

[Getting started with Chronograf](/chronograf/v1.5/introduction/getting-started/) guide offers step-by-step instructions for each of the requirements here.

* You've already downloaded and installed each component of the TICK stack (Telegraf, InfluxDB, Chronograf, and Kapacitor).
* Telegraf is configured to collect data using the InfluxDB [system statistics](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/system) input plugin and write data to your InfluxDB instance.
* [A Kapacitor connection has been created in Chronograf](/chronograf/v1.5/introduction/getting-started/#4-connect-chronograf-to-kapacitor).
* Slack is available and has been configured it as an [event handler](/chronograf/v1.5/troubleshooting/frequently-asked-questions/#what-kapacitor-event-handlers-are-supported-in-chronograf) in Chronograf.
See the [Configuring Kapacitor Event Handlers](/chronograf/v1.5/guides/configuring-alert-endpoints/) guide for detailed configuration instructions.

## Configuring Chronograf alert rules

Navigate to the **Rule Configuration** page by selecting the **Alerting** page and then clicking `Create Rule` in the top right corner.

![Navigate to Rule Configuration](/img/(/chronograf/v1.5/g-kap-rule-page.png)

The **Rule Configuration** page is used to create and edit your Chronogra alert rules.
The steps below guide you through the process of creating a Chronograf alert rule.

![Empty Rule Configuration](/img/(/chronograf/v1.5/g-kap-blank-rule.png)

### Step 1: Name the alert rule

Click **Untitled Rule** in the top left corner and name the rule.

For this example, the rule is named `Idle CPU Usage`:

![Name your rule](/img/(/chronograf/v1.5/g-kap-rule-name.png)

### Step 2: Select the time series data

Choose the time series data that you want the Chronograf alert rule to use.
Navigate through the **Databases**, **Measurements**, **Fields**, and **Tags** tabs to select the relevant data.

In this example, we select the `telegraf` [database](/{{< latest "influxdb" >}}/concepts/glossary/#database) and the `autogen` [retention policy](/{{< latest "influxdb" >}}/concepts/glossary/#retention-policy-rp), the `cpu` [measurement](/{{< latest "influxdb" >}}/concepts/glossary/#measurement), the `usage_idle` [field](/{{< latest "influxdb" >}}/concepts/glossary/#field), and no [tags](/{{< latest "influxdb" >}}/concepts/glossary/#tag).
The result is the InfluxQL [query](/{{< latest "influxdb" >}}/concepts/glossary/#query) in the image below.
Notice that Chronograf automatically sets a time range in the [`WHERE` clause](/{{< latest "influxdb" >}}/query_language/data_exploration/#the-where-clause).
Don't modify that for now. The time range is discussed in step four.

![Select your data](/img/(/chronograf/v1.5/g-kap-ts.png)

### Step 3: Select the alert type

Choose from three alert types in the `Rule Conditions` section of the Rule Configuration page.
The three alert types are:

* **Threshold**: Alert if the data cross a boundary
* **Relative**: Alert if the data change relative to the data in a different time range
* **Deadman**: Alert if InfluxDB receives no relevant data for the specified time duration

For this example, select the `Threshold` alert type.

### Step 4: Define the rule condition

Define the threshold condition:

![Create a condition](/img/(/chronograf/v1.5/g-kap-condition.png)

Moving across the inputs from right to left:

* `usage_idle`: The field key specified in the `Select a Time Series` section.
* `Less than`: The condition type. Chronograf supports several condition types.
* `80`: The threshold number. The system sends an alert when the `usage_idle` data cross that boundary.

The graph shows a preview of the relevant data and the threshold number.
By default, the graph shows data from the past 15 minutes.
The time range selector in the top right corner adjusts the graph's time range.
Use this feature when determining a reasonable threshold number based on your data.

> **Note:**
We set the threshold number to `80` for demonstration purposes.
On our machine, setting the threshold for idle CPU usage to a high number ensures that we'll be able to see the alert in action.
In practice, you'd set the threshold number to better match the patterns in your data and your alert needs.

### Step 5: Select the event handler and configure the alert message

The **Alert Message** section on the **Rule Configuration** page determines where the system sends the alert (the event handler) and the text that accompanies the alert (the alert message).
Chronograf supports several [event handlers](/chronograf/v1.5/troubleshooting/frequently-asked-questions/#what-kapacitor-event-handlers-are-supported-in-chronograf).
Here, we choose to send alerts to Slack, specifically to the existing `#chronocats` channel.

The alert message is the text that accompanies an alert.
In this example, the alert message is `Your idle CPU usage is {{.Level}} at {{ index .Fields "value" }}. 😸`.
`{{.Level}}` is a template that evaluates to `CRITICAL` when the `usage_idle` data initially dip below 80% and `OK` when the `usage_idle` data first return to 80% or above.
The `{{ index .Fields "value" }}` template prints the relevant [field value](/{{< latest "influxdb" >}}/concepts/glossary/#field-value) that triggered the alert.

![Specify event handler and alert message](/img/(/chronograf/v1.5/g-kap-alertmessage.png)

> **Note:**
There's no need to include a Slack channel in the `Alert Message` section if you specified a default channel in the [initial Slack configuration](/chronograf/v1.5/guides/configuring-alert-endpoints/).
If you did not include a default channel in the initial configuration or if you'd like to send alerts to a non-default channel, specify an alternative Slack channel in this section.

### Step 6: Save the alert rule

Click **Save Rule** in the top right corner and navigate to the **Alert Rule** page to see your rule.
Notice that you can easily enable and disable the rule by toggling the checkbox in the **Enabled** column.

![See the alert rule](/img/(/chronograf/v1.5/g-kap-rule-page-ii.png)

Next, move on to the section below to experience your alert rule in action.

## Viewing alerts in practice

### Step 1: Create some load on your system

The purpose of this step is to generate enough load on your system to trigger an alert.
More specifically, your idle CPU usage must dip below `80%`.
On the machine that's running Telegraf, enter the following command in the terminal to start some `while` loops:

```
while true; do i=0; done
```

Let it run for a few seconds or minutes before terminating it.
On most systems, kill the script by using `Ctrl+C`.

### Step 2: Visit Slack

Go to the Slack channel that you specified in the previous section.
In this example, it's the `#chronocats` channel.

Assuming the first step was successful, `#chronograf` should reveal at least two alert messages:

* The first alert message indicates that your idle CPU usage was `CRITICAL`, that is, it dipped below `80%`.
The specific [field value](/{{< latest "influxdb" >}}/concepts/glossary/#field-value) that triggered the alert is `69.59999999998138`.
* The second alert message indicates that your idle CPU usage returned to an `OK` level of `80%` or above.
The specific field value that triggered the alert is `99.0981963931105`.

![See the alerts](/img/(/chronograf/v1.5/g-kap-slack.png)

That's it! You've successfully used Chronograf to configure an alert rule to monitor your idle CPU usage.
