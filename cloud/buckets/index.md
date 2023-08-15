---
title: Buckets - Object Storage
sidebarTitle: "Buckets"
slug: cloud/buckets
product: ""
description: ""
searchable: true
lifecycle: general_availability
childPageOrder:
  - cloud/buckets/what-is-a-bucket
  - cloud/buckets/limits
---

Stedi Buckets lets you store files on the Stedi platform. You can upload and download files with the SDK, the CLI, the UI, or Stedi SFTP.

- Stedi Buckets is a key-value store. Think file system, not database.
- Storage space is unlimited.
- Data is encrypted at rest and in transit.
- Your bucket is not accessible outside of your Stedi account.

Read [What is a bucket?](/docs/cloud/buckets/what-is-a-bucket) for more information or dive right in with the links below.

## Use cases

You can use Stedi buckets in the following scenarios:

**Configure partnership settings in Stedi Core:** Set up a [bucket connection](/docs/core/configuration/connections) for Core to retrieve inbound files and deliver outbound files into a bucket for a specific partnership.

- **Retrieve files that were uploaded to a Stedi SFTP server.** For example, your trading partner uses SFTP to upload purchase orders and you read the purchase orders from the underlying bucket.
- **Read and write files using Stedi Functions.** You can use events to trigger functions automatically whenever a file is written to a bucket.
- **Access files using the Stedi SDK.** For example, you could use the Buckets SDK to upload files to a bucket from your local machine or from within a function.

## Getting started

Before you can use Stedi Buckets, you need to take care of two things:

- [Create a Stedi-account](https://www.stedi.com/terminal/sign-up) (free and no credit card required).
- [Create an API key](https://www.stedi.com/app/settings/api-keys).

## Reference

- [SDK: Using Stedi Buckets from code](/docs/sdk)
- [CLI: Using Stedi Buckets from the command line](https://www.npmjs.com/package/@stedi/cli)
- [Limits](/docs/limits#buckets)
