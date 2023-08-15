---
title: Date/time functions
slug: cloud/mappings/jsonata/jsonata-functions/date-time
product: ''
description: ''
searchable: true
childPageOrder: []
---
## $now

**Signature:** `$now([picture [, timezone]])`

**Parameters:**

* `picture` - If the optional `picture` string is supplied, then the timestamp is formatted occording to the representation specified in that string.

The behaviour of this function is consistent with the two-argument version of the XPath/XQuery function [fn:format-dateTime](https://www.w3.org/TR/xpath-functions-31/#func-format-dateTime) as defined in the XPath F\&O 3.1 specification.  The picture string parameter defines how the timestamp is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#date-picture-string) as fn:format-dateTime.

* `timezone` - If the optional `timezone` string is supplied, then the formatted timestamp will be in that timezone.

The `timezone` string should be in the format "±HHMM", where ± is either the plus or minus sign and HHMM is the offset in hours and minutes from UTC.  Positive offset for timezones east of UTC, negative offset for timezones west of UTC.

Generates a UTC timestamp in ISO 8601 compatible format and returns it as a string.  All invocations of `$now()` within an evaluation of an expression will all return the same timestamp value.

If the optional `picture` and `timezone` parameters are supplied, then the current timestamp is formatted as described by the [`$fromMillis()`](https://www.stedi.com/docs/cloud/mappings/jsonata/jsonata-functions/date-time#dollarfrommillis) function.

**Examples**

| Expression | Result                     |
|------------|----------------------------|
| $now()     | "2017-05-15T15:12:59.152Z" |

## $millis

**Signature:** `$millis()`

Returns the number of milliseconds since the Unix *Epoch* (1 January, 1970 UTC) as a number.  All invocations of `$millis()` within an evaluation of an expression will all return the same value.

**Examples**

| Expression | Result        |
|------------|---------------|
| $millis()  | 1502700297574 |

## $fromMillis

**Signature:** `$fromMillis(number [, picture [, timezone]])`

**Parameters:**

* `number` - A number representing milliseconds since the Unix *Epoch* (1 January, 1970 UTC).
* `picture` - If the optional `picture` string is supplied, then the timestamp is formatted occording to the representation specified in that string.

The behaviour of this function is consistent with the two-argument version of the XPath/XQuery function [fn:format-dateTime](https://www.w3.org/TR/xpath-functions-31/#func-format-dateTime) as defined in the XPath F\&O 3.1 specification.  The picture string parameter defines how the timestamp is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#date-picture-string) as fn:format-dateTime.

* `timezone` - If the optional `timezone` string is supplied, then the formatted timestamp will be in that timezone.

The `timezone` string should be in the format "±HHMM", where ± is either the plus or minus sign and HHMM is the offset in hours and minutes from UTC.  Positive offset for timezones east of UTC, negative offset for timezones west of UTC.

Convert the `number` representing milliseconds since the Unix *Epoch* (1 January, 1970 UTC) to a formatted string representation of the timestamp  as specified by the `picture` string.

If the optional `picture` parameter is omitted, then the timestamp is formatted in the [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) format.

If the optional `picture` string is supplied, then the timestamp is formatted occording to the representation specified in that string.
The behaviour of this function is consistent with the two-argument version of the XPath/XQuery function [fn:format-dateTime](https://www.w3.org/TR/xpath-functions-31/#func-format-dateTime) as defined in the XPath F\&O 3.1 specification.  The picture string parameter defines how the timestamp is formatted and has the [same syntax](https://www.w3.org/TR/xpath-functions-31/#date-picture-string) as fn:format-dateTime.

If the optional `timezone` string is supplied, then the formatted timestamp will be in that timezone.  The `timezone` string should be in the
format "±HHMM", where ± is either the plus or minus sign and HHMM is the offset in hours and minutes from UTC.  Positive offset for timezones
east of UTC, negative offset for timezones west of UTC.

**Examples**

| Expression                                                       | Result                     |
|------------------------------------------------------------------|----------------------------|
| $fromMillis(1510067557121)                                       | "2017-11-07T15:12:37.121Z" |
| $fromMillis(1510067557121, '\[M01]/\[D01]/\[Y0001] \[h#1]:\[m01]\[P]') | "11/07/2017 3:12pm"        |
| $fromMillis(1510067557121, '\[H01]:\[m01]:\[s01] \[z]', '-0500')     | "10:12:37 GMT-05:00"       |

## $toMillis

**Signature:** `$toMillis(timestamp [, picture])`

**Parameters:**

* `timestamp` - A formatted timestamp string.

If the optional `picture` string is not specified, then the format of the timestamp is assumed to be [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). An error is thrown if the string is not in the correct format.

* `picture` - If the `picture` string is specified, then the format is assumed to be described by this picture string using the [same syntax](https://www.w3.org/TR/xpath-functions-31/#date-picture-string) as the XPath/XQuery function [fn:format-dateTime](https://www.w3.org/TR/xpath-functions-31/#func-format-dateTime), defined in the XPath F\&O 3.1 specification.

Convert a `timestamp` string to the number of milliseconds since the Unix *Epoch* (1 January, 1970 UTC) as a number. This function is provided by Stedi and isn't a part of the JSONata standard library.

If the optional `picture` string is not specified, then the format of the timestamp is assumed to be [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html). An error is thrown if the string is not in the correct format.

If the `picture` string is specified, then the format is assumed to be described by this picture string using the [same syntax](https://www.w3.org/TR/xpath-functions-31/#date-picture-string) as the XPath/XQuery function [fn:format-dateTime](https://www.w3.org/TR/xpath-functions-31/#func-format-dateTime), defined in the XPath F\&O 3.1 specification.

**Examples**

| Expression                            | Result        |
|---------------------------------------|---------------|
| $toMillis("2017-11-07T15:07:54.972Z") | 1510067274972 |

## $convertDateTime

**Signature:** `$convertDateTime(str, sourceFormat, targetFormat [, sourceTimezone [, targetTimezone]])`

**Parameters:**

* `str` - An input string with date to be formatted
* `sourceFormat` - Date format of the input string using [using unicode tokens](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table)
* `targetFormat` - Format of the output string using [using unicode tokens](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table)
* `sourceTimezone` - Specifies source date's timezone if the date is in format without the zone offset e.g.: +01:00 or Z
* `targetTimezone` - If specified, function will also convert date's timezone to the targeted one.

Parses the `str` according to the `sourceFormat` and returns a formatted string according to `targetFormat`.
If `targetTimezone` is specified, function will also convert date's timezone to desired one. If `str` is in format without timezone defined, please use `sourceTimezone` to specify its timezone. Without it being specified, function will assume UTC timezone. If both `str` and `sourceTimezone` will be specified, zone offset in `str` will take a precedence over `sourceTimezone`.
Both formats must be specified [using unicode tokens](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table).
This function is provided by Stedi and isn't a part of the JSONata standard library.

Note there are 4 tokens that might cause confusion:

* `D` and `DD` that represent the day of a year (1, 2, ..., 365, 366) are often confused with `d` and `dd` that represent the day of a month (1, 2, ..., 31).
* `y` and `yyyy` that represent the local week-numbering year (44, 01, 00, 17) are often confused with `yy` and `yyyy` that represent the calendar year.

On top of the `$convertDateTime` function, we also provide popular date/time formats as constants under `$dateTime` object:

| Variable                | Value                          |
|-------------------------|--------------------------------|
| $dateTime.RFC3339       | "yyyy-MM-dd'T'HH:mm:ssXXX"     |
| $dateTime.RFC3339Millis | "yyyy-MM-dd'T'HH:mm:ss.SSSXXX" |
| $dateTime.EDIDate       | "yyMMdd"                       |
| $dateTime.EDIDateLong   | "yyyyMMdd"                     |

**Example**

| Function call                                                                                                | Returned value              |
| -------------------------------------------------------------------------------------------------------------| --------------------------- |
| $convertDateTime("20140919", "yyyyMMdd", "yyyy-MM-dd")                                                       | "2014-09-19"                |
| $convertDateTime("2021-01-02T12:00:00Z", $dateTime.RFC3339, "yyyy-MM-dd")                                    | "2021-01-02"                |
| $convertDateTime("2021-01-02T12:00:00+00:00", $dateTime.RFC3339, "yyyy-MM-dd")                               | "2021-01-02"                |
| $convertDateTime("210102", $dateTime.EDIDate, $dateTime.RFC3339)                                             | "2021-01-02T12:00:00Z"      |
| $convertDateTime("15:00 2nd January 2021", "HH:mm do MMMM yyyy", "yyyy-MM-dd")                               | "2021-01-02"                |
| $convertDateTime("2021-01-01T01:00:00-11:00", $dateTime.RFC3339, $dateTime.RFC3339, null, "UTC")             | "2021-01-01T12:00:00Z"      |
| $convertDateTime("2021-01-01T01:00:00Z", $dateTime.RFC3339, $dateTime.RFC3339, null, "Asia/Bangkok")         | "2021-01-01T08:00:00+07:00" |
| $convertDateTime("2021-01-01T01:00:00Z", $dateTime.RFC3339, $dateTime.RFC3339, "UTC", "America/New_York")    | "2020-12-31T20:00:00-05:00" |
| $convertDateTime("2021-01-01T01:00:00", "yyyy-MM-dd'T'HH:mm:ss", $dateTime.RFC3339, "EST", "UTC")            | "2021-01-01T06:00:00Z"      |

## $currentDateTime

**Signature:** `$currentDateTime(format [, timezone])`

**Parameters:**

* `format` - Date format of the input string using [using unicode tokens](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table)
* `timezone` - Optional, specifies the timezone in which the date/time is generated. Defaults to UTC.

Returns a current date and time formatted string according to `format`. Format must be specified [using unicode tokens](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table).
Optionally, a timezone can be specified. If not specified, the timezone is assumed to be UTC.
This function is provided by Stedi and isn't a part of the JSONata standard library.

Note there are 4 tokens that might cause confusion:

* `D` and `DD` that represent the day of a year (1, 2, ..., 365, 366) are often confused with `d` and `dd` that represent the day of a month (1, 2, ..., 31).
* `y` and `yyyy` that represent the local week-numbering year (44, 01, 00, 17) are often confused with `yy` and `yyyy` that represent the calendar year.

On top of the `$currentDateTime` function, we also provide popular date/time formats as constants under `$dateTime` object:

| Variable                | Value                          |
|-------------------------|--------------------------------|
| $dateTime.RFC3339       | "yyyy-MM-dd'T'HH:mm:ssXXX"     |
| $dateTime.RFC3339Millis | "yyyy-MM-dd'T'HH:mm:ss.SSSXXX" |
| $dateTime.EDIDate       | "yyMMdd"                       |
| $dateTime.EDIDateLong   | "yyyyMMdd"                     |

**Example**

| Function call                             | Returned value             |
| ----------------------------------------- | ---------------------------|
| $currentDateTime("yyyy-MM-dd")            | "2022-02-09"               |
| $currentDateTime($dateTime.EDIDate)       | "220209"                   |
| $currentDateTime($dateTime.RFC3339Millis) | "2022-02-09T08:23:52.000Z" |
| $currentDateTime("yyyy-MM-dd", "EST")     | "2022-02-09"               |
