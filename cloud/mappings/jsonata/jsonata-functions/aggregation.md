---
title: Aggregation functions
slug: cloud/mappings/jsonata/jsonata-functions/aggregation
product: ''
description: ''
searchable: true
childPageOrder: []
---
## $sum

**Signature:** `$sum(array)`

**Parameters:**

*   `array` - An array of numbers.

Returns the arithmetic sum of an array of numbers.  It is an error if the input array contains an item which isn't a number.

**Example**

| Expression        | Result |
|-------------------|--------|
| $sum(\[5,1,3,7,4]) | 20     |

## $max

**Signature:** `$max(array)`

**Parameters:**

*   `array` - An array of numbers.

Returns the maximum number in an array of numbers.  It is an error if the input array contains an item which isn't a number.

**Example**

| Expression        | Result |
|-------------------|--------|
| $max(\[5,1,3,7,4]) | 7      |

## $min

**Signature:** `$min(array)`

**Parameters:**

*   `array` - An array of numbers.

Returns the minimum number in an array of numbers.  It is an error if the input array contains an item which isn't a number.

**Example**

| Expression        | Result |
|-------------------|--------|
| $min(\[5,1,3,7,4]) | 1      |

## $average

**Signature:** `$average(array)`

**Parameters:**

*   `array` - An array of numbers.

Returns the mean value of an array of numbers.  It is an error if the input array contains an item which isn't a number.

**Example**

| Expression            | Result |
|-----------------------|--------|
| $average(\[5,1,3,7,4]) | 4      |
