---
title: Array functions
slug: cloud/mappings/jsonata/jsonata-functions/array
product: ''
description: ''
searchable: true
childPageOrder: []
---
## $count

**Signature:** `$count(array)`

**Parameters:**

*   `array` - An array to process.

If the `array` parameter is not an array, but rather a value of another JSON type, then the parameter is treated as a singleton array containing that value, and this function returns `1`.

Returns the number of items in the `array` parameter. If the `array` parameter is not an array, but rather a value of another JSON type, then the parameter is treated as a singleton array containing that value, and this function returns `1`.

If `array` is not specified, then the context value is used as the value of array.

**Examples**

| Expression        | Result |
|-------------------|--------|
| $count(\[1,2,3,1]) | 4      |
| $count("hello")   | 1      |

## $append

**Signature:** `$append(array1, array2)`

**Parameters:**

*   `array1` - The first array to append the second argument to.

If this parameter is not an array, then it is treated as a singleton array containing that value.

*   `array2` - The second array to append to the first argument.

If this parameter is not an array, then it is treated as a singleton array containing that value.

Returns an array containing the values in `array1` followed by the values in `array2`. If either parameter is not an array, then it is treated as a singleton array containing that value.

**Examples**

| Expression                | Result             |
|---------------------------|--------------------|
| $append(\[1,2,3], \[4,5,6]) | \[1,2,3,4,5,6]      |
| $append(\[1,2,3], 4)       | \[1,2,3,4]          |
| $append("Hello", "World") | \["Hello", "World"] |

## $sort

**Signature:** `$sort(array, [, function])`

**Parameters:**

*   `array` - An array to sort.
*   `function` - If a comparator `function` is supplied, then is must be a function that takes two parameters:

`function(left, right)`

This function gets invoked by the sorting algorithm to compare two values `left` and `right`. If the value of left should be placed after the value of `right` in the desired sort order, then the function must return Boolean `true` to indicate a swap. Otherwise it must return `false`.

Returns an array containing all the values in the `array` parameter, but sorted into order. If no `function` parameter is supplied, then the `array` parameter must contain only numbers or only strings, and they will be sorted in order of increasing number, or increasing unicode codepoint respectively.

If a comparator `function` is supplied, then is must be a function that takes two parameters:

`function(left, right)`

This function gets invoked by the sorting algorithm to compare two values `left` and `right`. If the value of left should be placed after the value of `right` in the desired sort order, then the function must return Boolean `true` to indicate a swap. Otherwise it must return `false`.

**Example**

```
$sort(Account.Order.Product, function($l, $r) {
  $l.Description.Weight > $r.Description.Weight
})
```

This sorts the products in order of increasing weight.

The sorting algorithm is stable which means that values within the original array which are the same according to the comparator function will remain in the original order in the sorted array.

## $reverse

**Signature:** `$reverse(array)`

**Parameters:**

*   `array` - An array to reverse.

Returns an array containing all the values from the `array` parameter, but in reverse order.

**Examples**

| Expression                   | Result             |
|------------------------------|--------------------|
| $reverse(\["Hello", "World"]) | \["World", "Hello"] |
| \[1..5] ~> $reverse()         | \[5, 4, 3, 2, 1]    |

## $shuffle

**Signature:** `$shuffle(array)`

**Parameters:**

*   `array` - An array to shuffle.

Returns an array containing all the values from the `array` parameter, but shuffled into random order.

**Examples**

| Expression           | Result                      |
|----------------------|-----------------------------|
| $shuffle(\[1..9])     | \[6, 8, 2, 3, 9, 5, 1, 4, 7] |

## $distinct

**Signature:** `$distinct(array)`

**Parameters:**

*   `array` - An array to process.

Returns an array containing all the values from the `array` parameter, but with any duplicates removed. Values are tested for deep equality as if by using the [equality operator](https://docs.jsonata.org/comparison-operators#equals).

**Examples**

| Expression                                          | Result                          |
|-----------------------------------------------------|---------------------------------|
| $distinct(\[1,2,3,3,4,3,5])                          | \[1, 2, 3, 4, 5]                 |
| $distinct(Account.Order.Product.Description.Colour) | \[ "Purple", "Orange", "Black" ] |

## $zip

**Signature:** `$zip(array1, ...)`

**Parameters:**

*   `array1` - An array to zip.

Returns a convolved (zipped) array containing grouped arrays of values from the `array1` ... `arrayN` arguments from index 0, 1, 2, etc.

This function accepts a variable number of arguments. The length of the returned array is equal to the length of the shortest array in the arguments.

**Examples**

| Expression                  | Result                |
|-----------------------------|-----------------------|
| $zip(\[1,2,3], \[4,5,6])      | \[\[1,4] ,\[2,5], \[3,6]] |
| $zip(\[1,2,3],\[4,5],\[7,8,9]) | \[\[1,4,7], \[2,5,8]]    |
