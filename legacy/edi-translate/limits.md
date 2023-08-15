---
title: EDI Translate Limits
slug: /legacy/edi-translate/limits
sidebarTitle: Limits
product: ""
description: "Request and response limits for Stedi’s EDI Translate API"
searchable: false
childPageOrder: []
---

The EDI Translate API has several limits on how you can use the service. If you are impacted or blocked by any of these restrictions, please [let us know](https://www.stedi.com/docs/support), and we would be happy to help solve your issue.

| Resource or operation       | Limit           | Can be increased |
| --------------------------- | --------------- | ---------------- |
| Requests per second         | 25              | Yes              |
| Maximum concurrent requests | 100             | Yes              |
| Maximum request size        | 6 megabytes[^1] | No[^2]           |
| Maximum response size       | 6 megabytes[^1] | No[^2]           |

[^1]: The request and response sizes are limited by the size of the HTTP request. You can receive more data if you use gzip compression on your request. Most HTTP clients support compression. You can enable it by setting the `Accept-Encoding` header to `gzip`. When translating EDI to JSON with compression enabled, the maximum response size represents the size of the compressed JSON response.

[^2]: [Stedi Core](https://www.stedi.com/docs/core) supports parsing of large files. For more information, visit [Core documentation](https://www.stedi.com/docs/core).
