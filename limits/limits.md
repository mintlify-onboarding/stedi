---
title: Limits
slug: limits
product: ""
description: ""
searchable: true
childPageOrder: []
---

This page contains limits information for the latest versions of all Stedi products.

## Core

Core has no hard restriction on the maximum file size you can attempt to process. There is an upper bound to what Core’s parser can process successfully, but that depends on a number of factors.

| Dimension                                    | Estimated limit      |
| -------------------------------------------- | -------------------- |
| File size                                    | ~500 MB <sup>1</sup> |
| Largest single transaction set within a file | ~130 MB <sup>2</sup> |

If your file fails to parse without a guide, try it again with a guide; in certain cases, using a guide will allow for slightly higher limits. In the very unlikely event that it still fails, drop us a note – we can help you implement a workaround that allows for virtually unbounded transaction set and file size. If you’re able to provide a failing sample, our engineers can use it to ultimately improve our parsing logic to handle the file without a workaround.

<sup>1. An EDI file can contain multiple interchanges, and each interchange can contain multiple transaction sets. The limit of what Core can process is generally dictated by the size of the largest EDI transaction set within the file, not by the file size itself.</sup>

<sup>2. This can vary depending on the complexity of the transaction set, the complexity of the guide, the number of elements in the transaction set, and other variables.</sup>

## Guides

The following service limits apply to Guides service. If you are impacted or blocked by any of these restrictions, please let us know and we would be happy to help meet your needs.

| Resource or operation                       | Limit              | Can be increased |
| ------------------------------------------- | ------------------ | ---------------- |
| Maximum number of guides per account        | 500                | Yes              |
| Maximum number of public guides per account | 25                 | Yes              |
| Maximum number of EDI samples per guide     | 12                 | No               |
| Maximum EDI sample size                     | 20 KB              | No               |
| Maximum character count in EDI Inspector    | 100,000 characters | No               |

## Functions

Stedi functions have the following restrictions:

- You can only define functions in JavaScript or another language that compiles to JavaScript, such as TypeScript.
- Functions does not provide a directly accessible API. You must access functions through the SDK or CLI.
- The maximum execution time of a function is 15 minutes. Functions have a 3-second timeout unless otherwise specified.
- Infrequently used functions can experience a cold start when a burst of activity occurs. Larger, more complex functions can take longer to warm up after inactivity, which causes initial latency.

### Technical details

| Dimension                                     | Default limit | Can be increased |
| --------------------------------------------- | ------------- | ---------------- |
| Maximum execution duration (timeout)          | 900s (15 min) | Yes              |
| Maximum concurrent invocations                | 1,000         | Yes              |
| Maximum request/response size (synchronous)   | 6 MB          | No               |
| Maximum request/response size (asynchronous)  | 256 KB        | No               |
| Maximum function package bundle size (zipped) | 50 MB         | No               |
| Memory available in Function runtime          | 1024 MB       | Yes              |
| Maximum size of all environment variables     | 4 KB          | No               |
| Temporary local disk storage                  | 512 MB        | Yes              |
| Maximum log retention period                  | 10 years      | Yes              |

If your needs are not met because of these limitations, please [contact us](/contact).

## Mappings

The following service limits are configured across Mappings APIs. If you are impacted or blocked by any of these restrictions, please let us know and we would be happy to help meet your needs.

| Resource or operation                   | Limit     | Can be increased |
| --------------------------------------- | --------- | ---------------- |
| Maximum number of mappings per account  | 1000      | Yes              |
| Requests per second                     | 1,000     | Yes              |
| Requests per day                        | 1,000,000 | Yes              |
| Mapping source JSON Schema              | 300 KB    | No               |
| Mapping target JSON Schema              | 300 KB    | No               |
| Mapping JSONata expression              | 300 KB    | No               |
| Lookup tables combined size             | 300 KB    | No               |
| Maximum mapped document size            | 4 MB      | No               |
| Maximum mapping operation response size | 4 MB      | No               |
| Maximum mapping operation duration      | 7 seconds | Yes              |

## Buckets

The Buckets service has a few limits in place on usage. If you are impacted or blocked by any of these restrictions, please let us know and we would be happy to help meet your needs.

| Limit                             |             |
| --------------------------------- | ----------- |
| Maximum buckets per Stedi account | 100         |
| Maximum objects per bucket        | unlimited   |
| Maximum object size               | 5 Terabytes |

## Stash

Unless noted otherwise, limits can be increased upon request. To request a limit increase, [contact support](https://www.stedi.com/contact).

### Keyspaces

_Limits for `Keyspaces`, which can contain many `Key-Value Pairs`_

| Resource or operation                       | Default limit                                                            | Can be increased |
| ------------------------------------------- | ------------------------------------------------------------------------ | ---------------- |
| Maximum number of `Keyspaces`               | 10                                                                       | Yes              |
| `Keyspace` size                             | No practical limit in terms of bytes or Key-Value Pairs                  | N/A              |
| Throttle per account, across all Stash APIs | 25 requests per second (RPS)                                             | Yes              |
| `Keyspace Name` length                      | 250 characters                                                           | No               |
| `Keyspace Name` allowed characters          | Alphanumeric (`a-zA-Z0-9`), underscore (`_`), period (`.`), hyphen (`-`) | No               |

### Key-Value Pairs

_Limits for `Key-Value Pairs`, which are made up of `Keys` and `Values`_

| Resource or operation                   | Default limit                                                                           | Can be increased |
| --------------------------------------- | --------------------------------------------------------------------------------------- | ---------------- |
| `Key` length                            | 255 characters                                                                          | No               |
| `Key-Value Pair` size                   | 8 KB ([more details](#how-is-the-size-of-a-key-value-pair-calculated))                  | No               |
| Precision of Number types in `Values`   | 38 significant figures ([more details](#what-is-the-accepted-range-for-numeric-values)) | No               |
| Supported string characters in `Values` | UTF-8                                                                                   | No               |

#### How is the size of a `Key-Value Pair` calculated?

- The size of a `Key-Value Pair` is equal to: i) the number of characters in the `Key`, plus ii) the `Value` size, plus iii) 2 additional bytes of overhead.
- The `Value` size is equal to: i) the number of bytes in the JSON attribute names (encoded with UTF-8 binary encoding), plus ii) the sizes of the JSON values.

You can estimate the size of JSON values with the following guidelines:

- String values are encoded with UTF-8 binary encoding. The size of a string is the number of UTF-8-encoded bytes.
- Number values are variable length, with up to 38 significant digits. Leading and trailing zeroes are trimmed. The size of a number value is approximately 1 byte per two significant digits + 1 byte.
- Boolean or Null values are 1 byte in size.
- Lists incur 3 bytes of overhead, regardless of their contents. The size of a List value is the sum of the size of its elements + 1 byte per element. The size of an empty List is just the 3 bytes of overhead.
- Maps incur 3 bytes of overhead, regardless of their contents. The size of a Map value is the sum of the size of its string keys + the sum of the size of its elements + 1 byte per element. The size of an empty Map is just the 3 bytes of overhead.

For storage billing purposes, each Stash value requires 100 bytes of overhead for indexing.

#### What is the accepted range for Numeric values?

Numbers can have up to 38 significant figures.

- Positive range: 1E-130 to 9.9999999999999999999999999999999999999E+125
- Negative range: -9.9999999999999999999999999999999999999E+125 to -1E-130
