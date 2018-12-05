---
title: sum() function
description: The sum() function computes the sum of non-null records in specified columns.
menu:
  flux_0_7:
    name: sum
    parent: Aggregates
    weight: 1
---

The `sum()` function computes the sum of non-null records in specified columns.

_**Function type:** Aggregate_  
_**Output data type:** Integer, UInteger, or Float (inherited from column type)_

```js
sum(columns: ["_value"])
```

## Parameters

### columns
Specifies a list of columns on which to operate.
Defaults to `["_value"]`.

_**Data type:** Array of strings_

## Examples
```js
from(bucket: "telegraf/autogen")
  |> range(start: -5m)
  |> filter(fn: (r) =>
    r._measurement == "cpu" AND
    r._field == "usage_system"
  )
  |> sum()
```

<hr style="margin-top:4rem"/>

##### Related InfluxQL functions and statements:
[SUM()](/influxdb/latest/query_language/functions/#sum)  
