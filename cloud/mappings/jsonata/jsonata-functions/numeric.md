---
title: Numeric functions
slug: cloud/mappings/jsonata/jsonata-functions/numeric
product: ''
description: ''
searchable: true
childPageOrder: []
---

>**Note:** It is not recommended to use JSONata for floating point arithmetic, such as financial calculations. Floating-point arithmetic can introduce rounding errors, which can accumulate and lead to incorrect results. Visit our [JSONata Playground](https://stedi.link/UKXfsxe) for an example.
>We recommend representing monetary values as integers (e.g. cents) and performing calculations on those integers.

## $number

**Signature:** `$number(arg)`

**Parameters:**

*   `arg` - An argument to be cast to number.

Casts the `arg` parameter to a number using the following casting rules

*   Numbers are unchanged
*   Strings that contain a sequence of characters that represent a legal JSON number are converted to that number
*   Hexadecimal numbers start with `0x`, Octal numbers with `0o`, binary numbers with `0b`
*   Boolean `true` casts to `1`, Boolean `false` casts to `0`

If `arg` is not specified (i.e. this function is invoked with no arguments), then the context value is used as the value of `arg`.

**Examples**

| Expression                          | Result          |
|-------------------------------------|-----------------|
| $number("5")                        | 5               |
| $number("0x12")                     | 0x18            |
| \["1", "2", "3", "4", "5"].$number() | \[1, 2, 3, 4, 5] |

## $abs

**Signature:** `$abs(number)`

**Parameters:**

*   `number` - A number to get an absolute value of.

Returns the absolute value of the `number` parameter, i.e. if the number is negative, it returns the positive value.

If `number` is not specified (i.e. this function is invoked with no arguments), then the context value is used as the value of `number`.

**Examples**

| Expression | Result |
|------------|--------|
| $abs(5)    | 5      |
| $abs(-5)   | 5      |

## $floor

**Signature:** `$floor(number)`

**Parameters:**

*   `number` - The source number.

Returns the value of `number` rounded down to the nearest integer that is smaller or equal to `number`.

If `number` is not specified (i.e. this function is invoked with no arguments), then the context value is used as the value of `number`.

**Examples**

| Expression   | Result |
|--------------|--------|
| $floor(5)    | 5      |
| $floor(5.3)  | 5      |
| $floor(5.8)  | 5      |
| $floor(-5.3) | -6     |

## $ceil

**Signature:** `$ceil(number)`

**Parameters:**

*   `number` - The source number.

Returns the value of `number` rounded up to the nearest integer that is greater than or equal to `number`.

If `number` is not specified (i.e. this function is invoked with no arguments), then the context value is used as the value of `number`.

**Examples**

| Expression  | Result |
|-------------|--------|
| $ceil(5)    | 5      |
| $ceil(5.3)  | 6      |
| $ceil(5.8)  | 6      |
| $ceil(-5.3) | -5     |

## $round

**Signature:** `$round(number [, precision])`

**Parameters:**

*   `number` - The source number.
*   `precision` - The `precision` parameter (which must be an integer) species the number of decimal places to be present in the rounded number.

If `precision` is not specified then it defaults to the value `0` and the number is rounded to the nearest integer.
If `precision` is negative, then its value specifies which column to round to on the left side of the decimal place

Returns the value of the `number` parameter rounded to the number of decimal places specified by the optional `precision` parameter.

The `precision` parameter (which must be an integer) species the number of decimal places to be present in the rounded number.   If `precision` is not specified then it defaults to the value `0` and the number is rounded to the nearest integer.  If `precision` is negative, then its value specifies which column to round to on the left side of the decimal place

This function uses the [Round half to even](https://en.wikipedia.org/wiki/Rounding#Round_half_to_even) strategy to decide which way to round numbers that fall exactly between two candidates at the specified precision.  This strategy is commonly used in financial calculations and is the default rounding mode in IEEE 754.

**Examples**

| Expression          | Result |
|---------------------|--------|
| $round(123.456)     | 123    |
| $round(123.456, 2)  | 123.46 |
| $round(123.456, -1) | 120    |
| $round(123.456, -2) | 100    |
| $round(11.5)        | 12     |
| $round(12.5)        | 12     |
| $round(125, -1)     | 120    |

## $power

**Signature:** `$power(base, exponent)`

**Parameters:**

*   `base` - The base number (<code>base<sup>exponent</sup></code>).
*   `exponent` - The exponent number (<code>base<sup>exponent</sup></code>).

Returns the value of `base` raised to the power of `exponent` (<code>base<sup>exponent</sup></code>).

If `base` is not specified (i.e. this function is invoked with one argument), then the context value is used as the value of `base`.

An error is thrown if the values of `base` and `exponent` lead to a value that cannot be represented as a JSON number (e.g. Infinity, complex numbers).

**Examples**

| Expression      | Result         |
|-----------------|----------------|
| $power(2, 8)    | 256            |
| $power(2, 0.5)  | 1.414213562373 |
| $power(2, -2)   | 0.25           |

## $sqrt

**Signature:** `$sqrt(number)`

**Parameters:**

*   `number` - The source number.

Returns the square root of the value of the `number` parameter.

If `number` is not specified (i.e. this function is invoked with one argument), then the context value is used as the value of `number`.

**Examples**

| Expression      | Result         |
|-----------------|----------------|
| $sqrt(4)        | 2              |
| $sqrt(2)        | 1.414213562373 |

## $random

**Signature:** `$random()`

Returns a pseudo random number greater than or equal to zero and less than one `(0 ≤ n < 1)`

**Examples**

| Expression      | Result          |
|-----------------|-----------------|
| $random()       | 0.7973541067127 |
| $random()       | 0.414213562373  |
| $random()       | 0.6558078550072 |

## $formatNumber

**Signature:** `$formatNumber(number, picture [, options])`

**Parameters:**

*   `number` - The source number.
*   `picture` - Format of the desired decimal representation.

The picture string parameter defines how the number is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#syntax-of-picture-string) as fn:format-number.

*   `options` - The optional third argument `options` is used to override the default locale specific formatting characters such as the decimal separator.

If supplied, this argument must be an object containing name/value pairs specified in the [decimal format](https://www.w3.org/TR/xpath-functions-31/#defining-decimal-format) section of the XPath F\&O 3.1 specification.

Casts the `number` to a string and formats it to a decimal representation as specified by the `picture` string.

The behaviour of this function is consistent with the XPath/XQuery function [fn:format-number](https://www.w3.org/TR/xpath-functions-31/#func-format-number) as defined in the XPath F\&O 3.1 specification.  The picture string parameter defines how the number is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#syntax-of-picture-string) as fn:format-number.

The optional third argument `options` is used to override the default locale specific formatting characters such as the decimal separator.  If supplied, this argument must be an object containing name/value pairs specified in the [decimal format](https://www.w3.org/TR/xpath-functions-31/#defining-decimal-format) section of the XPath F\&O 3.1 specification.

**Examples**

| Expression                              | Result      |
|-----------------------------------------|-------------|
| $formatNumber(12345.6, '#,###.00')      | "12,345.60" |
| $formatNumber(1234.5678, "00.000e0")    | "12.346e2"  |
| $formatNumber(34.555, "#0.00;(#0.00)")  | "34.56"     |
| $formatNumber(-34.555, "#0.00;(#0.00)") | "(34.56)"   |

## $formatBase

**Signature:** `$formatBase(number [, radix])`

**Parameters:**

*   `number` - The source number.
*   `radix` - The optional `radix` parameter represents the mathematical base of the `number`.

If `radix` is not specified, then it defaults to base 10. `radix` can be between 2 and 36, otherwise an error is thrown.

Casts the `number` to a string and formats it to an integer represented in the number base specified by the `radix` argument.  If `radix` is not specified, then it defaults to base 10.  `radix` can be between 2 and 36, otherwise an error is thrown.

**Examples**

| Expression            | Result    |
|-----------------------|-----------|
| $formatBase(100, 2)   | "1100100" |
| $formatBase(2555, 16) | "9fb"     |

## $formatInteger

**Signature:** `$formatInteger(number, picture)`

**Parameters:**

*   `number` - The source number.
*   `picture` - Format of the desired integer representation.

The picture string parameter defines how the number is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#func-format-integer) as fn:format-integer.

Casts the `number` to a string and formats it to an integer representation as specified by the `picture` string.

The behaviour of this function is consistent with the two-argument version of the XPath/XQuery function [fn:format-integer](https://www.w3.org/TR/xpath-functions-31/#func-format-integer) as defined in the XPath F\&O 3.1 specification.  The picture string parameter defines how the number is formatted and has the same syntax as fn:format-integer.

**Examples**

| Expression                | Result                                        |
|---------------------------|-----------------------------------------------|
| $formatInteger(2789, 'w') | "two thousand, seven hundred and eighty-nine" |
| $formatInteger(1999, 'I') | "MCMXCIX"                                     |

## $parseInteger

**Signature:** `$parseInteger(string, picture)`

**Parameters:**

*   `string` - The source string to parse.
*   `picture` - Format of the integer representation.

The picture string parameter has the same format as `$formatInteger`, which has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#func-format-integer) as fn:format-integer.

Parses the contents of the `string` parameter to an integer (as a JSON number) using the format specified by the `picture` string.
The picture string parameter has the same format as `$formatInteger`. Although the XPath specification does not have an equivalent
function for parsing integers, this capability has been added to JSONata.

**Examples**

| Expression                                                          | Result   |
|---------------------------------------------------------------------|----------|
| $parseInteger("twelve thousand, four hundred and seventy-six", 'w') | 12476    |
| $parseInteger('12,345,678', '#,##0')                                | 12345678 |
