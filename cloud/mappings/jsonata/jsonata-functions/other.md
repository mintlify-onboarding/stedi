---
title: Other functions
slug: cloud/mappings/jsonata/jsonata-functions/other
product: ""
description: ""
searchable: true
childPageOrder: []
---

## $lookupTable

**Signature:** `$lookupTable(table, filterKeys, filterPredicates[, options])`

**Parameters:**

- `table` - Source lookup table. All lookup tables are available inside $tables global variable
- `filterKeys` - String or array of strings of keys to filter on
- `filterPredicates` - One or more predicates to filter on. If `filterKeys` is an array, `filterPredicates` must be an array of the same length
- `options` - Object with optional properties:
  - `wildcard` - Wildcard string to use for matching. E.g. if set to "\*", then "express_aero" will match "express\_\*".

Returns row from a lookup table where all filter conditions are satisfied.
Supports [wildcard matching](https://www.stedi.com/docs/cloud/mappings/jsonata/common-mapping-expressions#lookup-table-wildcards).
This function is provided by Stedi and isn't a part of the JSONata standard library.

**Example**

Given following lookup table named "shipping":

| Key | Value       | Preferred |
| --- | ----------- | --------- |
| 1   | express     | false     |
| 2   | standard    | false     |
| 2   | overnight   | true      |
| 3   | express\_\* | false     |

| Expression                                                                             | Result            |
| -------------------------------------------------------------------------------------- | ----------------- |
| {'$lookupTable($tables.shipping, "Key", "1").Value'}                                   | "express"         |
| {'$lookupTable($tables.shipping, "Key", "2").Preferred'}                               | ["false", "true"] |
| {'$lookupTable($tables.shipping, ["Key", "Preferred"], ["2", "true"]).Value'}          | "overnight"       |
| {'$lookupTable($tables.shipping, "Value", "express_aero", { "wildcard": "\*" }).Key'}  | "3"               |
| {'$lookupTable($tables.shipping, "Value", "express_other", { "wildcard": "\*" }).Key'} | "3"               |

## $uuid

**Signature:** `$uuid()`

Create a version 4 (random) UUID.
This function is provided by Stedi and isn't a part of the JSONata standard library.

**Example**

| Expression | Result                                 |
| ---------- | -------------------------------------- |
| $uuid()    | "1a28e35f-d420-4b95-ad09-1bb4b39821dc" |
