---
title: EDI Translate Pricing
slug: legacy/edi-translate/pricing
sidebarTitle: Pricing
product: ""
description: "Pricing information for Stedi’s EDI Translate API"
searchable: false
childPageOrder: []
---

The EDI Translate API is billed based on usage. There are no minimum fees, monthly commitments, or upfront costs to use this product.

Usage is calculated based on request count and MB processed.

A request is defined as a single call to the EDI Translate API, regardless of how many transactions (e.g. ST segments) are in the payload.

## Pricing tiers

### API request count pricing

The first 1,000 requests per month are free. Each API request includes a 10 KB data allowance.

| **Request Count per Month** | **Price**             |
| --------------------------- | --------------------- |
| First 1,000                 | Free                  |
| 1,000 to 250,000            | $0.007500 per request |
| 250,000 to 500,000          | $0.006750 per request |
| 500,000 to 1,000,000        | $0.006375 per request |
| Above 1,000,000             | $0.006000 per request |

### API bytes processed per request pricing

Each API request includes a 10 KB data allowance, and you're only charged for bytes processed above the 10 KB allowance per request. The first 5 MB of data processed per month are free.

| **Data Processed per Month** | **Price**                         |
| ---------------------------- | --------------------------------- |
| Up to 5 MB\*                 | Free                              |
| Above 5 MB                   | $0.0075 for each additional 50 KB |

[Contact us](https://www.stedi.com/contact) if you have a question on pricing.

\*1 MB = 1024 KB or 1,048,576 bytes
