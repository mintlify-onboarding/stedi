---
title: Legacy Pricing
sidebarTitle: "Legacy Pricing"
slug: legacy/pricing
product: ""
description: ""
searchable: false # By design, we don't want this page to be searchable
---

# Legacy Pricing

## Core

[Core](https://www.stedi.com/docs/core) allows you to build your own end-to-end integrations fast without becoming an EDI expert.

### Partner profiles

- 1 active partner profile included
- $150 per month per additional active partner profile

### EDI Parsing (EDI to JSON)

|                     |                                                    |
| ------------------- | -------------------------------------------------- |
| **File executions** | **$0.008 per file (includes first 10KB)**          |
| Additional bytes    | $0.00016 per KB of EDI input (above 10KB per file) |
| Transaction sets    | $0.0016 per transaction set                        |

### EDI Generation (JSON to EDI)

|                  |                                                         |
| ---------------- | ------------------------------------------------------- |
| **API requests** | **$0.008 per request (includes first 10KB)**            |
| Additional bytes | $0.000016 per KB of JSON input (above 10KB per request) |
| Transaction sets | $0.0016 per transaction set                             |

### Storage

|                           |                        |
| ------------------------- | ---------------------- |
| Total GB stored per month | $0.19 per GB per month |

### Public Guides

Includes 15 public guides

## Mappings

[Mappings](https://www.stedi.com/docs/cloud/mappings) is a tool that maps JSON documents from one structure to another to integrate with any pipeline.

### API requests

The first 1,000 requests per month are included in the platform fee.

| Request count per month | Price              |
| ----------------------- | ------------------ |
| First 1,000             | Included           |
| Above 1,000             | $0.001 per request |

## SFTP

[SFTP](https://www.stedi.com/docs/sftp) allows you to exchange data with trading partners over a familiar protocol. Provision users instantly and send an unlimited number of files, with no operational overhead.

### Files uploaded

The first 500 files uploaded per month are included in the platform fee.

| File uploads per month | Price                 |
| ---------------------- | --------------------- |
| First 500              | Included              |
| Above 500              | $0.75 per 1,000 files |

### Files downloaded

The first 500 files downloaded per month are included in the platform fee.

| File downloads per month | Price                 |
| ------------------------ | --------------------- |
| First 500                | Included              |
| Above 500                | $0.75 per 1,000 files |

### Data uploaded

The first 5 MB uploaded per month are included in the platform fee.

| Total MB uploaded per month | Price         |
| --------------------------- | ------------- |
| Up to 5 MB                  | Included      |
| Above 5 MB                  | $0.512 per GB |

### Data downloaded

The first 5 MB downloaded per month are included in the platform fee.

| Total MB downloaded per month | Price         |
| ----------------------------- | ------------- |
| Up to 5 MB                    | Included      |
| Above 5 MB                    | $0.512 per GB |

## AS2

[AS2](https://www.stedi.com/docs/legacy/as2) lets you integrate with businesses that require you to share data using AS2, or offer AS2 as a connectivity option to your own trading partners.

| Category          | Price               |
| ----------------- | ------------------- |
| AS2 server        | $0.40 per hour      |
| Messages sent     | $0.0125 per message |
| Messages received | $0.0125 per message |

- You only need a server to receive AS2 messages. You can send AS2 messages without creating a server.
- Message size of up to 50 MB is included. For messages larger than 50 MB, every additional 50 MB chunk is counted as another unit (message) rounded down to the nearest chunk.

## Buckets

[Buckets](https://www.stedi.com/docs/cloud/buckets) provides scalable object storage. Interact with data in a bucket via SFTP, read and write documents, and build powerful integrations based on bucket events.

### Storage

The first GB stored per month is included in the platform fee.

| Total GB stored per month | Price                  |
| ------------------------- | ---------------------- |
| First 1 GB (31 GB-Days)   | Included               |
| Above 1 GB (31 GB-Days)   | $0.19 per GB per month |
|                           |                        |

### PUT, COPY, and POST requests

The first 1,000 requests per month are included in the platform fee.

| Request count per month | Price                    |
| ----------------------- | ------------------------ |
| First 1,000             | Included                 |
| Above 1,000             | $0.50 per 1,000 requests |

### GET, SELECT, LIST, and all other requests

The first 1,000 requests per month are included in the platform fee.

| Request count per month | Price                    |
| ----------------------- | ------------------------ |
| First 1,000             | Included                 |
| Above 1,000             | $0.05 per 1,000 requests |

### Data transit fee

The first 10 MB per month are included in the platform fee.

| Total MB downloaded per month | Price          |
| ----------------------------- | -------------- |
| Up to 10 MB                   | Included       |
| Above 10 MB                   | $0.0512 per GB |

### Data transfer out to the Internet

The first 10 MB per month are included in the platform fee.

| Total MB downloaded per month | Price         |
| ----------------------------- | ------------- |
| Up to 10 MB                   | Included      |
| Above 10 MB                   | $0.128 per GB |

Storage charges are calculated on a daily basis at a rate of $0.006144
per GB per day. The quoted price is for a month with 31 days.

1 GB = 1024 MB, and 1 MB = 1024 KB or 1,048,576 bytes.

## Functions

[Functions](https://www.stedi.com/docs/cloud/functions) lets you run custom code to power your integrations.

### Function invocations

There is a charge per invocation request.

| Invocations per month | Price                    |
| --------------------- | ------------------------ |
| First 1,000           | Included                 |
| Above 1,000           | $0.02 per 1,000 requests |

### Duration

There is a charge for the duration it takes for your code to execute.

| Duration per month | Price                  |
| ------------------ | ---------------------- |
| First 300          | Included               |
| Above 300          | $0.00167 per GB-second |

### Data Transfer OUT from Stedi Functions to the Internet

Any data sent from Stedi Functions to the Internet will incur a data transfer charge.

| Data transfer out | Price         |
| ----------------- | ------------- |
| First 10 MB       | Included      |
| Above 10 MB       | $0.128 per GB |

Data transfer is charged on a per MB basis at the rate of
$0.128 / 1,024 = $0.000125 per MB.

## Stash

[Stash](https://www.stedi.com/docs/cloud/stash) is a key-value store that you can use to power your B2B integration or EDI workflow.

### Reads

Stash charges one read request unit for each read (up to 4 KB).

| Read request units per month | Price                              |
| ---------------------------- | ---------------------------------- |
| First 1,000                  | Included                           |
| Above 1,000                  | $0.04 per 1,000 read request units |

### Writes

Stash charges one write request unit for each write (up to 1 KB).

| Write request units per month | Price                               |
| ----------------------------- | ----------------------------------- |
| First 1,000                   | Included                            |
| Above 1,000                   | $0.13 per 1,000 write request units |

### Storage

The first 10 MB stored per month are free.

| Total MB stored per month | Price          |
| ------------------------- | -------------- |
| First 10 MB (310 MB-Days) | Included       |
| Above 10 MB (310 MB-Days) | $0.0186 per MB |

Storage charges are calculated on a daily basis at a rate of
$0.0006 per MB per day. The quoted price is for a month with 31 days.

1 MB = 1024 KB, and 1 KB = 1024 bytes.
