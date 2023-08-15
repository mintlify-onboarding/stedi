---
title: Boolean functions
slug: cloud/mappings/jsonata/jsonata-functions/boolean
product: ''
description: ''
searchable: true
childPageOrder: []
---
## $boolean

**Signature:** `$boolean(arg)`

**Parameters:**

*   `arg` - An argument to be cast to a Boolean.

Casts the argument to a Boolean using the following rules:

| Argument type | Result |
| ------------- | ------ |
| Boolean | unchanged |
| string: empty | `false`|
| string: non-empty | `true` |
| number: 0 | `false`|
| number: non-zero | `true` |
| null | `false`|
| array: empty | `false` |
| array: contains a member that casts to `true` |  `true` |
| array: all members cast to `false` |  `false` |
| object: empty | `false` |
| object: non-empty | `true` |
| function | `false` |

## $not

**Signature:** `$not(arg)`

**Parameters:**

*   `arg` - An argument to be cast to a Boolean and inverted.

Returns Boolean NOT on the argument.  `arg` is first cast to a boolean.

## $exists

**Signature:** `$exists(arg)`

**Parameters:**

*   `arg` - An argument to chech the existence of.

Returns Boolean `true` if the arg expression evaluates to a value, or `false` if the expression does not match anything (e.g. a path to a non-existent field reference).
