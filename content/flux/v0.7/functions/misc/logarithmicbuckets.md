---
title: logarithmicBuckets() function
description: The logarithmicBuckets() function generates a list of exponentially separated floats.
menu:
  flux_0_7:
    name: logarithmicBuckets
    parent: Miscellaneous
    weight: 1
---

The `logarithmicBuckets()` function generates a list of exponentially separated floats.

_**Function type:** Miscellaneous_  
_**Output data type:** Array of floats_

```js
logarithmicBuckets(start:1.0, factor: 2.0, count: 10, infinity: true)
```

## Parameters

### start
The first value in the returned bucket list.

_**Data type:** Float_

### factor
The multiplier applied to each subsequent bucket.

_**Data type:** Float_

### count
The number of buckets to create.

_**Data type:** Integer_

### infinity
When `true`, adds an additional bucket with a value of positive infinity.
Defaults to `true`.

_**Data type:** Boolean_

## Examples
```js
logarithmicBuckets(start: 1.0, factor: 2.0, count: 10, infinty: true)

// Generated list: [1, 2, 4, 8, 16, 32, 64, 128, 256, 512, +Inf]
```
