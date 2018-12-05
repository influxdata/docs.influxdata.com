---
title: Release Notes/Changelog
menu:
  enterprise_kapacitor_1_5:
    weight: 0
    parent: About the Project
---

# Changelog

## v1.5.1 [2018-08-06]

Includes features and bug fixes from [Kapacitor 1.5.1](/kapacitor/v1.5/about_the_project/releasenotes-changelog/#v1-5-1-2018-08-06).

### Bugfixes

- Fix an issue to ensure alert deduplication is by topic.
  Previously, alert events were being erroneously deduplicated across the set of topics assigned to a node.
  Topic assignment changes with cluster size, so it is not a stable behavior.
  This change fixes the unintended behavior.

## v1.5.0 [2018-05-22]

Includes features and bug fixes from [Kapacitor 1.5.0](/kapacitor/v1.5/about_the_project/releasenotes-changelog/#v1-5-0-2018-05-17).

### Bugfixes

- Fix match condition being lost through rpc layer.

## v1.4.1 [2018-03-14]

Includes bugfixes from Kapacitor 1.4.1

### Bug fixes

* Fix `load` service not working when authentication is enabled.

## v1.4.0 [2017-12-08]

The v1.4.0 release has many new features, here is a list of some of the highlights:

- Load TICKscripts and alert handlers from a directory.
- Structed Logging  with a logging API endpoints to be able to tail logs for given tasks.
- Autoscale support for Docker Swarm and EC2 Autoscaling.
- Sideload data into your TICKscript streams from external sources.
- Fully customizable POST body for the alert POST handler and the httpPost node.

See the complete list of bug fixes and features below.

### Bugfixes

- Idle Barrier is dropping all messages when source has clock offset
- Fix oddly generated TOML for mqtt & httppost

## v1.4.0-rc3 [2017-12-04]

### Bugfixes

- Fix issues where log API checked the wrong header for the desired content type.

## v1.4.0-rc2 [2017-11-28]

### Features

- Add support for AWS EC2 autoscaling services.
- Add BarrierNode to emit BarrierMessage periodically

### Bugfixes

- Fix VictorOps "data" field being a string instead of actual JSON.
- Fix panic with MQTT toml configuration generation.

## v1.4.0-rc1 [2017-11-09]

### Features

- Add Previous state.
- Add support to persist replay status after it finishes.
- alert.post and https_post timeouts needed.
- Add subscriptions modes to InfluxDB subscriptions.
- Add linear fill support for QueryNode.
- Add MQTT Alert Handler
- Add built in functions to convert timestamps to integers
- BREAKING: Change over internal API to use message passing semantics.
  The breaking change is that the Combine and Flatten nodes previously, but erroneously, operated across batch boundaries; this has been fixed.
- Add support for Docker Swarm autoscaling services.
- Add bools field types to UDFs.
- Add stateless now() function to get the current local time.
- Add support for timeout, tags and service template in the Alerta AlertNode
- Add support for custom HTTP Post bodies via a template system.
- Add support for add the HTTP status code as a field when using httpPost
- Add logfmt support and refactor logging.
- Add ability to load tasks/handlers from dir.
  TICKscript was extended to be able to describe a task exclusively through a tickscript.
    * Tasks no longer need to specify their TaskType (Batch, Stream).
    * `dbrp` expressions were added to tickscript.
  Topic-Handler file format was modified to include the TopicID and HandlerID in the file.
  Load service was added; the service can load tasks/handlers from a directory.
- Update Go version to 1.9.1
- Add support for exposing logs via the API. API is released as a technical preview.
- Add support for {{ .Duration }} on Alert Message property.
- Add support for [JSON lines](https://en.wikipedia.org/wiki/JSON_streaming#Line-delimited_JSON) for steaming HTTP logs.
- Add new node Sideload, that allows loading data from files into the stream of data. Data can be loaded using a hierarchy.
- Promote Alert API to stable v1 path.
- Change WARN level logs to INFO level.

### Bugfixes

- Crash of Kapacitor on Windows x64 when starting a recording
- Allow for `.yml` file extensions in `define-topic-handler`
- Fix http server error logging.
- Fix bugs with stopping running UDF agent.
- Fix error messages for missing fields which are arguments to functions are not clear
- Fix bad PagerDuty test the required server info.
- Add SNMP sysUpTime to SNMP Trap service
- Fix panic on recording replay with HTTPPostHandler.
- Fix k8s incluster master api dns resolution
- Remove the pidfile after the server has exited.
- Logs API writes multiple http headers.
- Fix missing dependency in rpm package.
- Force tar owner/group to be root.
- Fixed install/remove of kapacitor on non-systemd Debian/Ubuntu systems.
  Fixes packaging to not enable services on RHEL systems.
  Fixes issues with recusive symlinks on systemd systems.
- Fix invalid default MQTT config.

## v1.3.3 [2017-08-11]

### Bugfixes

- Bypass auth when `pprof` is enabled.

## v1.3.2 [2017-08-08]

### Bugfixes

- Use details field from alert node in PagerDuty.

## v1.3.1 [2017-06-20]

### Bugfixes

- Fix inconsistent naming of node vs member.

## v1.3.0 [2017-06-19]

### Release Notes

With this release, Kapacitor Enterprise can be clustered and will deduplicate alerts generated within the cluster.

### Bugfixes

- Expose HTTP API advertise address.
- Fix panic if alerts are triggering during startup.
- Fix `ENV` overrides not working for text Unmarshaler types.

## v1.3.0-rc3 [2017-06-13]

### Bugfixes

- Fix `build.sh` to copy build artifacts back out of docker data volume.

## v1.3.0-rc2 [2017-06-13]

### Bugfixes

- Expose Advertise Address properly.

## v1.3.0-rc1 [2017-06-09]

### Features

- Add Alert deduplication for a cluster of Kapacitor Enterprise nodes.
- Add support for subscription modes.

### Bugfixes

- Fix data race in rendezvous hash type.

## v1.2.2 [2017-04-14]

### Bugfixes

- Fix InfluxDB token client not opening.

## v1.2.1 [2017-04-13]

### Release Notes

No Kapacitor Enterprise-specific changes, only upgrading to Kapacitor 1.2.1.
Contains the fix for stale credentials when querying InfluxDB.

## v1.2.0 [2017-01-24]

### Release Notes

No Kapacitor Enterprise-specific changes, only upgrading to Kapacitor 1.2.0.

## v1.1.1 [2016-12-06]

### Release Notes

No Kapacitor Enterprise-specific changes, only upgrading to Kapacitor 1.1.1.

## v1.1.0 [2016-11-08]

### Release Notes

### Bugfixes

- Update to add Kapacitor dynamic config support.

## v1.0.2 [2016-10-06]

### Release Notes

Update to Kapacitor 1.0.2

### Bugfixes

- Fail on startup if saving cluster/server IDs fails.
- Update to Kapacitor fixes for #954

## v1.0.1 [2016-09-26]

### Release Notes

Update to Kapacitor 1.0.1
No Kapacitor Enterprise-specific changes.

## v1.0.0 [2016-09-02]

### Release Notes

Update to Kapacitor 1.0.0

### Features

- Upgrade to Kapacitor 1.0.0-rc3
    NOTE: rc3 and the final 1.0.0 release are identical.

## v0.1.0 [2016-08-29]

### Release Notes

Update to Kapacitor 1.0.0-rc2

### Features

- Upgrade to Kapacitor 1.0.0-rc2

## v0.0.5 [2016-08-12]

### Release Notes

Update to lastest Kapacitor for Subscription improvements.

### Bugfixes

- Remove default user, only add it for tests.

## v0.0.4 [2016-08-10]

### Release Notes

### Features

### Bugfixes

- Fix PM permission conversion error for generic read/write privileges to dbs.

## v0.0.3 [2016-08-03]

### Release Notes

### Features

- Add token-based authentication for InfluxDB communication.

### Bugfixes

## v0.0.2 [2016-07-27]

### Release Notes

### Features

- Add mechanism for subscription token-based auth.

### Bugfixes

## v0.0.1 [2016-07-25]

### Release Notes

First release of Kapacitor Enterprise with basic authentication/authorization support.

### Features

- Auth Service Implementation. Simple boltdb/bcrypt backed users/auth.
- Entitlements and Enterprise registration.
- BREAKING: Search for valid configuration on startup in `~/.kapacitor` and `/etc/kapacitor/`.
    This is so that the `-config` CLI flag is not required if the configuration is found in a standard location.
    The configuration file being used is always logged to `STDERR`.
- Add support for using InfluxDB Enterprise meta for users backend.
