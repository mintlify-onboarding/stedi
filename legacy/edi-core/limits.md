---
title: Limits
slug: legacy/edi-core/limits
product: ""
description: ""
searchable: false
childPageOrder: []
---

The EDI Core API has a few limits on how you can use the service. If you are impacted, please let us know and we would be happy to work to help meet your needs. The request and response sizes are limited by the size of the HTTP request. You can receive more data if you set the `accept-encoding` header to `gzip`. The API can also accept a [gzip compressed request][compression].

| Limit                       |             | Can be increased |
| --------------------------- | ----------- | ---------------- |
| Requests per second         | 25          | Yes              |
| Maximum concurrent requests | 100         | Yes              |
| Maximum request size        | 4 megabytes | No               |
| Maximum response size       | 6 megabytes | No               |

[compression]: https://www.stedi.com/docs/edi-core/compression
