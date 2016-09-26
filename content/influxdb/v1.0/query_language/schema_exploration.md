---
title: Schema Exploration
menu:
  influxdb_1_0:
    weight: 20
    parent: query_language
---

InfluxQL is an SQL-like query language for interacting with data in InfluxDB.
The following sections cover useful query syntax for exploring your schema (that is, how you set up your time series data):

* [See all databases with `SHOW DATABASES`](/influxdb/v1.0/query_language/schema_exploration/#see-all-databases-with-show-databases)
* [Explore retention policies with `SHOW RETENTION POLICIES`](/influxdb/v1.0/query_language/schema_exploration/#explore-retention-policies-with-show-retention-policies)
* [Explore metaseries with `SHOW SERIES`](/influxdb/v1.0/query_language/schema_exploration/#explore-metaseries-with-show-series)
* [Explore measurements with `SHOW MEASUREMENTS`](/influxdb/v1.0/query_language/schema_exploration/#explore-measurements-with-show-measurements)
* [Explore tag keys with `SHOW TAG KEYS`](/influxdb/v1.0/query_language/schema_exploration/#explore-tag-keys-with-show-tag-keys)
* [Explore tag values with `SHOW TAG VALUES`](/influxdb/v1.0/query_language/schema_exploration/#explore-tag-values-with-show-tag-values)
* [Explore field keys with `SHOW FIELD KEYS`](/influxdb/v1.0/query_language/schema_exploration/#explore-field-keys-with-show-field-keys)

The examples below query data using [InfluxDB's Command Line Interface (CLI)](/influxdb/v1.0/tools/shell/).
See the [Querying Data](/influxdb/v1.0/guides/querying_data/) guide for how to directly query data with the HTTP API.

**Sample data**

This document uses the same sample data as the [Data Exploration](/influxdb/v1.0/query_language/data_exploration/) page.
The data are described and are available for download on the [Sample Data](/influxdb/v1.0/query_language/data_download/) page.

## See all databases with `SHOW DATABASES`
Get a list of all the databases in your system by entering:
```sql
SHOW DATABASES
```

CLI example:
```bash
> SHOW DATABASES
name: databases
---------------
name
NOAA_water_database
```

## Explore retention policies with `SHOW RETENTION POLICIES`
The `SHOW RETENTION POLICIES` query lists the existing [retention policies](/influxdb/v1.0/concepts/glossary/#retention-policy-rp) on a given database, and it takes the following form:
```sql
SHOW RETENTION POLICIES ON <database_name>
```

CLI example:
```sql
> SHOW RETENTION POLICIES ON "NOAA_water_database"
```

CLI response:
```bash
name	    duration	 shardGroupDuration	 replicaN	 default
autogen	 0		       168h0m0s		          1		       true
```

The first column of the output contains the names of the different retention policies in the specified database.
The second column shows the retention policy's [duration](/influxdb/v1.0/concepts/glossary/#duration) and the third column shows the [shard group](/influxdb/v1.0/concepts/glossary/#shard-group) duration. The fourth column shows the [replication factor](/influxdb/v1.0/concepts/glossary/#replication-factor) of the retention policy and the fifth column specifies if the retention policy is the `DEFAULT` retention policy for the database.

The following example shows a hypothetical CLI response where there are four different retention policies in the database, and where the `DEFAULT` retention policy is `three_days_only`:

```bash
name		           duration	 shardGroupDuration	 replicaN	 default
autogen		        0		       168h0m0s		          1		       false
two_days_only	   48h0m0s		 24h0m0s			          1		       false
one_day_only	    24h0m0s		 1h0m0s			           1		       false
three_days_only	 72h0m0s		 24h0m0s			          1		       true
```

## Explore metaseries with `SHOW SERIES`
The `SHOW SERIES` query returns the distinct [metaseries](/influxdb/v1.0/concepts/glossary/#metaseries) in your database and takes the following form, where the `FROM` and `WHERE` clauses are optional:

```sql
SHOW SERIES [FROM <measurement_name> [WHERE <tag_key> [= '<tag_value>' | =~ <regular_expression>]]]
```

Return all metaseries in the database `NOAA_water_database`:
```sql
> SHOW SERIES
```

CLI response:  
```bash
key
average_temperature,location=coyote_creek
average_temperature,location=santa_monica
h2o_feet,location=coyote_creek
h2o_feet,location=santa_monica
h2o_pH,location=coyote_creek
h2o_pH,location=santa_monica
h2o_quality,location=coyote_creek,randtag=1
h2o_quality,location=coyote_creek,randtag=2
h2o_quality,location=coyote_creek,randtag=3
h2o_quality,location=santa_monica,randtag=1
h2o_quality,location=santa_monica,randtag=2
h2o_quality,location=santa_monica,randtag=3
h2o_temperature,location=coyote_creek
h2o_temperature,location=santa_monica
```

`SHOW SERIES` organizes its output similar to the [line protocol](/influxdb/v1.0/concepts/glossary/#line-protocol) format.
Everything before the first comma is the [measurement](/influxdb/v1.0/concepts/glossary/#measurement) name.
Everything after the first comma is either a [tag key](/influxdb/v1.0/concepts/glossary/#tag-key) or a [tag value](/influxdb/v1.0/concepts/glossary/#tag-value).

From the output above you can see that the data in the database `NOAA_water_database` have five different measurements and 14 different metaseries.
The measurements are `average_temperature`, `h2o_feet`, `h2o_pH`, `h2o_quality`, and `h2o_temperature`.
Every measurement has the tag key `location` with the tag values `coyote_creek` and `santa_monica` - that makes 10 metaseries.
The measurement `h2o_quality` has the additional tag key `randtag` with the tag values `1`,`2`, and `3` - that makes 14 metaseries.

Return metaseries for a specific measurement:
```sql
> SHOW SERIES FROM "h2o_quality"
```

CLI response:
```bash
key
h2o_quality,location=coyote_creek,randtag=1
h2o_quality,location=coyote_creek,randtag=2
h2o_quality,location=coyote_creek,randtag=3
h2o_quality,location=santa_monica,randtag=1
h2o_quality,location=santa_monica,randtag=2
h2o_quality,location=santa_monica,randtag=3
```

Return metaseries for a specific measurement and tag set:
```sql
> SHOW SERIES FROM "h2o_quality" WHERE "location" = 'coyote_creek'
```

CLI response:
```bash
key
h2o_quality,location=coyote_creek,randtag=1
h2o_quality,location=coyote_creek,randtag=2
h2o_quality,location=coyote_creek,randtag=3
```

## Explore measurements with `SHOW MEASUREMENTS`
The `SHOW MEASUREMENTS` query returns the [measurements](/influxdb/v1.0/concepts/glossary/#measurement) in your database and it takes the following form:
```sql
SHOW MEASUREMENTS [WITH MEASUREMENT <regular_expression>] [WHERE <tag_key> [= '<tag_value>' | =~ <regular_expression>]]
```

Return all measurements in the `NOAA_water_database` database:
```sql
> SHOW MEASUREMENTS
```

CLI response:
```bash
name: measurements
------------------
name
average_temperature
h2o_feet
h2o_pH
h2o_quality
h2o_temperature
```

From the output you can see that the database `NOAA_water_database` has five measurements: `average_temperature`, `h2o_feet`, `h2o_pH`, `h2o_quality`, and `h2o_temperature`.

Return measurements where the tag key `randtag` equals `1`:
```sql
> SHOW MEASUREMENTS WHERE "randtag" = '1'
```

CLI response:
```bash
name: measurements
------------------
name
h2o_quality
```

Only the measurement `h2o_quality` contains the tag set `randtag = 1`.

Use a regular expression to return measurements where the tag key `randtag` is a digit:
```sql
> SHOW MEASUREMENTS WHERE "randtag" =~ /\d/
```

CLI response:
```bash
name: measurements
------------------
name
h2o_quality
```

Use a regular expression with `WITH MEASUREMENT` to return all measurements that start with `h2o`:
```sql
> SHOW MEASUREMENTS WITH MEASUREMENT =~ /h2o.*/
```

CLI response:
```bash
name: measurements
------------------
name
h2o_feet
h2o_pH
h2o_quality
h2o_temperature
```

## Explore tag keys with SHOW TAG KEYS
`SHOW TAG KEYS` returns the [tag keys](/influxdb/v1.0/concepts/glossary/#tag-key) associated with each measurement and takes the following form, where the `FROM` clause is optional:
```sql
SHOW TAG KEYS [FROM <measurement_name>]
```

Return all tag keys that are in the database `NOAA_water_database`:
```sql
> SHOW TAG KEYS
```

CLI response:
```bash
name: average_temperature
-------------------------
tagKey
location

name: h2o_feet
--------------
tagKey
location

name: h2o_pH
------------
tagKey
location

name: h2o_quality
-----------------
tagKey
location
randtag

name: h2o_temperature
---------------------
tagKey
location
```

InfluxDB organizes the output by measurement.
Notice that each of the five measurements has the tag key `location` and that the measurement `h2o_quality` also has the tag key `randtag`.

Return the tag keys for a specific measurement:
```sql
> SHOW TAG KEYS FROM "h2o_temperature"
```

CLI response:
```bash
name: h2o_temperature
---------------------
tagKey
location
```

## Explore tag values with SHOW TAG VALUES
The `SHOW TAG VALUES` query returns the set of [tag values](/influxdb/v1.0/concepts/glossary/#tag-value) for a specific tag key across all measurements in the database.
```sql
SHOW TAG VALUES [FROM <measurement_name>] [WITH KEY [ = "<tag_key>" | IN ("<tag_key1>","<tag_key2")]] [WHERE <tag_key> [= '<tag_value>' | =~ <regular_expression>]]
```

Syntax notes:

* `SHOW TAG VALUES` supports regular expressions in the `WITH KEY` clause
* `SHOW TAG VALUES` requires a `WITH` clause if the query includes a `WHERE` clause

Return the tag values for a single tag key (`randtag`) across all measurements in the database `NOAA_water_database`:
```sql
> SHOW TAG VALUES WITH KEY = "randtag"
```

CLI response:
```bash
name: h2o_quality
-----------------
key	     value
randtag	 1
randtag	 3
randtag	 2
```

Return the tag values for the tag keys `location` or `randtag` for all measurements where the tag key `randtag` has tag values:
```sql
> SHOW TAG VALUES WITH KEY IN ("location","randtag") WHERE "randtag" =~ /./
```

CLI response:
```bash
name: h2o_quality
-----------------
key       value
location  coyote_creek
randtag   1
randtag   2
randtag   3
location  santa_monica
```

Return the tag values for all tag keys that do not include the letter `c`:
```
> SHOW TAG VALUES WITH KEY !~ /.*c.*/
name: h2o_quality
-----------------
key       value
randtag	  1
randtag	  2
randtag	  3
```

Return the tag values for the tag key `randtag` for a specific measurement in the `NOAA_water_database` database:
```sql
> SHOW TAG VALUES FROM "average_temperature" WITH KEY = "randtag"
```

CLI response:
```bash
```

The measurement `average_temperature` doesn't have the tag key `randtag` so InfluxDB returns nothing.

## Explore field keys with `SHOW FIELD KEYS`
The `SHOW FIELD KEYS` query returns the [field keys](/influxdb/v1.0/concepts/glossary/#field-key)
and [field value](/influxdb/v1.0/concepts/glossary/#field-value) data [types](/influxdb/v1.0/write_protocols/write_syntax/#data-types) across each measurement in the database.
It takes the following form, where the `FROM` clause is optional:

```sql
SHOW FIELD KEYS [FROM <measurement_name>]
```

> **Note:** A field's type can differ across
[shards](/influxdb/v1.0/concepts/glossary/#shard).
If your field has more than one type, `SHOW FIELD KEYS` returns the type that
occurs first in the following list: float, integer, string, boolean.

Return the field keys and field value data types across all measurements in the database `NOAA_water_database`:

```sql
> SHOW FIELD KEYS
```

CLI response:
```bash
name: average_temperature
-------------------------
fieldKey	  fieldType
degrees		  float

name: h2o_feet
--------------
fieldKey		          fieldType
level description	  string
water_level		       float

name: h2o_pH
------------
fieldKey	 fieldType
pH		      float

name: h2o_quality
-----------------
fieldKey	 fieldType
index		   float

name: h2o_temperature
---------------------
fieldKey	 fieldType
degrees		 float
```

Return the field keys in the measurement `h2o_feet` in the database `NOAA_water_database`:

```sql
> SHOW FIELD KEYS FROM "h2o_feet"
```

CLI response:

```bash
name: h2o_feet
--------------
fieldKey		          fieldType
level description	  string
water_level		       float
```
