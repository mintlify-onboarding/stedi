---
title: SFTP
slug: legacy/sftp
product: sftp
description: ""
searchable: true
lifecycle: legacy
childPageOrder:
  - legacy/sftp/accessing-files
  - legacy/sftp/polling
  - legacy/sftp/limits
  - legacy/sftp/endpoints
  - legacy/sftp/sdk
  - legacy/sftp/using-stedi-sftp-from-the-command-line
  - legacy/sftp/using-stedi-sftp-sdk
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

Stedi SFTP provides fully managed, serverless SFTP infrastructure that supports SFTP and FTPS protocols.You can use Stedi SFTP to securely exchange files with trading partners without having to worry about setting up servers or provisioning capacity. Stedi SFTP scales instantly, on demand to an unlimited number of trading partners and an unlimited number of files.

## How it works

Your Stedi account gives you access to a Stedi-hosted SFTP endpoint. In a couple of clicks, you can create a user
using [the SFTP UI](https://www.stedi.com/app/sftp). The user will have access to the SFTP endpoint, but only to
files that are within the directory that you assign to them. You can also create users programmatically at
scale [via the API](/docs/legacy/sftp/using-stedi-sftp-from-the-command-line)
or [using the SDK](/docs/legacy/sftp/using-stedi-sftp-sdk).

## Use cases

Here are some common use cases for SFTP:

- **Exchange files with your trading partners.** Create an SFTP user with a unique home directory for each of your
  trading partners, and share the credentials with them. They can then upload and download files in that directory through
  SFTP or FTPS.
- **Process uploaded documents using Stedi Functions.** You can [access uploaded documents](/docs/legacy/sftp/accessing-files) using Stedi Buckets.
  The bucket can be configured to [automatically invoke a Stedi Function whenever a new
  document is uploaded](/docs/legacy/sftp/accessing-files#bucket-notifications).

## Getting started

To get started with SFTP, use one of the following methods.

### Using the UI

- **[Create a Stedi account](/terminal/sign-up)** (free and no credit card required).
- **[Create an SFTP user in the UI](/app/sftp/create).**

### Using the command line

You can also do this [from the command line](/docs/legacy/sftp/using-stedi-sftp-from-the-command-line).

### Using the SDK

We publish a JavaScript SDK, which you can use to [create and manage SFTP users](/docs/legacy/sftp/using-stedi-sftp-sdk).

## Automatically provisioned resources:

When you create an SFTP user the following resources are automatically provisioned, if they don't already exist:

- An SFTP-enabled [Stedi bucket](/docs/cloud/buckets)
- The home directory specified for the user (for any home directory other than the root directory `/`)

## Permissions

SFTP users have a home directory. They can access any file or directory under their home directory, but none outside it.
In order to exchange files with your trading partners Alice and Bob, you could provision the following users:

### Alice

- description: `Trading partner Alice`
- home directory: `/alice`

### Bob

- description: `Trading partner Bob`
- home directory: `/bob`

Next, provision a root user which you can use to access files in each of the trading partners’ home directories.

### Root

- description: `Root user`
- home directory: `/`

Alice and Bob will only be able to upload and download files in their home directories. Root will have access to all files, and can
be used to upload and download documents for all trading partners.
