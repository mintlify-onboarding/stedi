---
title: Stash - Key/value store
sidebarTitle: "Stash"
slug: cloud/stash
product: stash
description: ''
searchable: true
childPageOrder:
  - cloud/stash/limits
  - cloud/stash/endpoints
  - cloud/stash/curl-postman-example
  - cloud/stash/javascript-example
---
## What is Stash?

Stash is a key-value store that you can use to power your B2B integration or EDI workflow. Use it to track documents processed, increment control numbers, or reference lookup tables, all using a simple API. There are no servers, clusters, networking, or replication to manage.

## What can Stash be used for?

Stash allows you to store *values* associated with *keys*, and later retrieve those *values* using the same *keys*. It can be used as a standalone data store for a wide variety of general use cases, or as part of an EDI workflow built on Stedi.

Common use cases for Stash include:

- Complete an end-to-end EDI integration after you have configured [Stedi Core](/docs/core) for a new trading partner. For example, you can use Stash to configure where you want to send processed data.
- Extend an EDI integration. For example, you might want to store information extracted from documents or APIs for later use, such as inventory or price catalogs.
- Store large mapping tables – for example, a directory with millions of entries that map vendor SKUs to internal SKUs.

## How does Stash work?

Stash allows you to create *Keyspaces* that can store and allow you to retrieve any number of *key-value pairs*. Stash automatically scales up or down your [Keyspace throughput capacity](/docs/limits#keyspaces), with no performance degradation. All Stash data is encrypted at rest and in transit, which eliminates the operational burden and complexity involved in protecting sensitive data. Stash is also architected to ensure high availability and data durability.

Stash is intentionally built with a minimal feature set. The fundamental concepts in Stash are *keyspaces*, *keys* and *values*. *Key-value pairs* are always scoped to a named *keyspace*, and all operations on *keys* and *values* must include the name of the *keyspace*. *Keyspace names* are unique in a Stedi account, and can only be reused once the *keyspace* has been deleted. *Key-value pairs* can be a maximum of 8 KB in size.

A keyspace can hold an unlimited amount of data, and system performance improves as a keyspace is accessed more often. We recommend minimizing the number of keyspaces used ([the default per-account limit](/docs/limits#stash) is 10, though that can be raised upon request), and only creating additional keyspaces when building multiple applications inside of one account, or when different teams each need to manage their own data. In order to avoid key conflicts when using the same keyspace for different use cases within a single application, we recommend prepending keys with a type identification or sub-application identifier.

Stash's interface is flexible and easy to use, with just a handful of [API operations](https://www.stedi.com/docs/api/stash):

- *Create keyspace*. Create a keyspace in which to store key-value pairs.
- *Get keyspace*. See information about a keyspace.
- *Delete keyspace*. Remove a keyspace.
- *List keyspaces*. List of your existing keyspaces.
- *Set value*. Add or update a key-value pair.
- *Get value*. Retrieve the current value of a given key.
- *Delete value*. Remove a key-value pair from a keyspace.
- *List values*. Retrieve a list of key-value pairs from a keyspace.

A stash `value` can be a valid JSON value of type:

- `string`
- `number` - see [limits](https://www.stedi.com/docs/limits#stash) for numeric constraints.
- `boolean`
- `array`
- `object`

For more complete documentation on the data model and API, see the [API reference](/docs/api/stash).

## Read consistency

All read (`GetValue`, `ListValues`) operations are *strongly consistent*. Reads always return the most up-to-date data, reflecting the updates from all prior write (`SetValue`, `DeleteValue`) operations that were successful.

## Ordering

For the `ListValues` operation, there is no guarantee on the order of results returned.

## Examples

- [curl/Postman example](https://www.stedi.com/docs/cloud/stash/curl-postman-example)
- [JavaScript example](https://www.stedi.com/docs/cloud/stash/javascript-example)
- [Control numbers example](https://github.com/Stedi-Demos/control-numbers)
