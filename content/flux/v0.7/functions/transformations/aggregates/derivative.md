---
title: derivative() function
description: The derivative() function computes the rate of change per unit of time between subsequent non-null records.
menu:
  flux_0_7:
    name: derivative
    parent: Aggregates
    weight: 1
---

The `derivative()` function computes the rate of change per [`unit`](#unit) of time between subsequent non-null records.
The output table schema will be the same as the input table.

_**Function type:** Aggregate_  
_**Output data type:** Float_

```js
derivative(unit: 100ms, nonNegative: false, columns: ["_value"], timeSrc: "_time")
```

## Parameters

### unit
The time duration used when creating the derivative.

_**Data type:** Duration_

### nonNegative
Indicates if the derivative is allowed to be negative.
When set to `true`, if a value is less than the previous value, it is assumed the previous value should have been a zero.

_**Data type:** Boolean_

### columns
A list of columns on which to compute the derivative.
Defaults to `["_value"]`

_**Data type:** Array of strings_

### timeSrc
The column containing time values.
Defaults to `"_time"`.

_**Data type:** String_

## Examples
```js
from(bucket: "telegraf/autogen")
  |> range(start: -5m)
  |> derivative(unit: 100ms, nonNegative: true)
```

<hr style="margin-top:4rem"/>

##### Related InfluxQL functions and statements:
[DERIVATIVE()](/influxdb/latest/query_language/functions/#derivative)
