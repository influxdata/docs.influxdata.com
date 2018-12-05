---
title: increase() function
description: The increase() function calculates the total non-negative difference between values in a table.
menu:
  flux_0_7:
    name: increase
    parent: Aggregates
    weight: 1
---

The `increase()` function calculates the total non-negative difference between values in a table.
A main use case is tracking changes in counter values which may wrap over time when they hit a threshold or are reset.
In the case of a wrap/reset, the function assumes the absolute delta between two points is at least their non-negative difference.

_**Function type:** Aggregate_  
_**Output data type:** Float_

```js
increase(columns: ["_values"])
```

## Parameters

### columns
The list of columns for which the increase is calculated.
Defaults to `["_value"]`.

_**Data type:** Array of strings_

## Examples
```js
from(bucket: "telegraf/autogen")
  |> range(start: -24h)
  |> filter(fn: (r) => r._measurement == "system" AND r._field == "n_users")
  |> increase()
```

## Function definition
```js
increase = (tables=<-, columns=["_value"]) =>
	tables
		|> difference(nonNegative: true, columns:columns)
		|> cumulativeSum()
```
