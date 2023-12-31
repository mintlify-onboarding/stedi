---
title: Object functions
slug: cloud/mappings/jsonata/jsonata-functions/object
product: ""
description: ""
searchable: true
childPageOrder: []
---

## $keys

**Signature:** `$keys(object)`

**Parameters:**

- `object` - The source object to get the keys from.

Returns an array containing the keys in the object. If the argument is an array of objects, then the array returned contains a de-duplicated list of all the keys in all of the objects.

## $lookup

**Signature:** `$lookup(object, key)`

**Parameters:**

- `object` - An object to search for a `key` in.

If the `object` argument is an array of objects, then all of the objects in the array are searched

- `key` - A key to search for.

Returns the value associated with `key` in `object`. If the first argument is an array of objects, then all of the objects in the array are searched, and the values associated with all occurrences of `key` are returned.

## $spread

**Signature:** `$spread(object)`

**Parameters:**

- `object` - An object to convert.

If the `object` parameter is an array of objects, then the resultant array contains an object for every key/value pair in every object in the supplied array.

Splits an object containing key/value pairs into an array of objects, each of which has a single key/value pair from the input object. If the parameter is an array of objects, then the resultant array contains an object for every key/value pair in every object in the supplied array.

## $merge

**Signature:** `$merge(object1, ...)`

**Parameters:**

- `object1` - An object to merge.

Merges an array of objects into a single object containing all the key/value pairs from each of the objects in the input array. If any of the input objects contain the same key, then the returned object will contain the value of the last one in the array. It is an error if the input array contains an item that is not an object.

## $each

**Signature:** `$each(object, function)`

**Parameters:**

- `object` - An object to process.
- `function` - The `function` parameter will get invoked with two arguments:

`function(value, name)`

where the `value` parameter is the value of each name/value pair in the object and `name` is its name. The `name` parameter is optional.

Returns an array containing the values return by the `function` when applied to each key/value pair in the `object`.

The `function` parameter will get invoked with two arguments:

`function(value, name)`

where the `value` parameter is the value of each name/value pair in the object and `name` is its name. The `name` parameter is optional.

| Expression                                            | Result                                                                 |
| ----------------------------------------------------- | ---------------------------------------------------------------------- |
| {'$each(Address, function($v, $k) {$k & ": " & $v})'} | `[ "Street: Hursley Park", "City: Winchester", "Postcode: SO21 2JN" ]` |

## $error

**Signature:** `$error(message)`

**Parameters:**

- `message` - An error message to throw.

Deliberately throws an error with an optional `message`

## $assert

**Signature:** `$assert(condition, message)`

**Parameters:**

- `condition` - A condition expression to assert.
- `message` - An error message to throw on `condition` assertion failure

If `condition` is true, the function returns undefined. If the condition is false, an exception is thrown with the `message` as the message of the exception.

## $type

**Signature:** `$type(value)`

**Parameters:**

- `value` - A value to get the type of.

Evaluates the type of `value` and returns one of the following strings:

- `"null"`
- `"number"`
- `"string"`
- `"boolean"`
- `"array"`
- `"object"`
- `"function"` Returns (non-string) `undefined` when `value` is `undefined`.
