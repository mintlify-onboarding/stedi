---
title: Pricing
slug: legacy/edi-core/pricing
product: ""
description: ""
searchable: false
childPageOrder: []
---

The EDI Core API is billed based on usage. There are no minimum fees, monthly commitments, or upfront costs to use this product.

Usage of the /translate endpoint is billed based on request count and MB processed.

A request is defined as a single call to the EDI Core API, regardless of how many transactions (e.g. ST segments) are in the payload.

## Pricing tiers

### API request count pricing

The first 1,000 requests per month are free.

| **Request Count per Month** | **Price**           |
| --------------------------- | ------------------- |
| First 1,000                 | Free                |
| Above 1,000                 | $0.0075 per request |

### API bytes processed per request pricing

The first 5 MB of data processed per month are free.

| **Data Processed per Month** | **Price**      |
| ---------------------------- | -------------- |
| Up to 5 MB\*                 | Free           |
| Above 5 MB                   | $0.0512 per MB |

[Contact us](https://www.stedi.com/contact) if you have a question on pricing or for a volume discount (minimum 250K requests per month)

\*1 MB = 1024 KB or 1,048,576 bytes

## Examples

### Shipment status (214) feed

The EDI Core `/translate` endpoint is used to convert batched X12 EDI 214s from a shipping partner into JEDI bound for a retailers' ERP. Each EDI 214 document contains 300 transactions, resulting in an average file size of 150 KB. The shipping partner sends a batch every 30 minutes, every day of the week, resulting in 1,488 EDI 214s every month.

```text
First 1,000 requests = $0.00
Next 488 requests = 488 * $0.0075 = $3.66
Total for all requests = $3.66

Total bytes processed = 150 KB * 1,488 requests = 217.97 MB
First 5 MB = $0.00
Next 212.97 MB = 212.97 * $0.0512 = $10.90
Total for all requests = $10.90

$3.66 + $10.90 = $14.56 total
$14.56 / 1,488 requests = $0.009788 per request
```

### Medium volume purchase order (850) feed

The EDI Core `/translate` endpoint is used to convert JEDI 850s to X12 EDI 850s to generate purchase orders for vendors. The retailer generates 25,500 X12 EDI 850s each month, and each JEDI file is an average of 1.5 KB in size.

```text
First 1,000 requests = $0.00
Next 24,500 requests = 24,500 * $0.0075 = $183.75
Total for all requests = $183.75

Total bytes processed = 1.5 KB * 25,500 requests = 37.35 MB
First 5 MB = $0.00
Next 32.35 MB = 32.35 * $0.0512 = $1.66
Total for all requests = $1.66

$183.75 + $1.66 = $185.41 total
$185.41 / 25,500 requests = $0.007271 per request
```

### Hourly inventory (846) feed

The EDI Core `/translate` endpoint is used to convert X12 EDI 846 Inventory Inquiry/Advice to the JEDI equivalent in order to exchange inventory data with trading partners. A customer receives 10,000 KB (10MB) EDI 846s once an hour every day, totaling 720 files over the course of a month.

```text
First 1,000 requests = $0.00
Total for all requests = $0.00

Total bytes processed = 10,000 KB * 720 requests = 7,031.25 MB
First 5 MB = $0.00
Next 7,026.25 MB = 7,026.25 * $0.0512 = $359.74
Total for all requests = $359.74

$0.00 + $359.74 = $359.74 total
$359.74 / 720 requests = $0.499644 per request
```
