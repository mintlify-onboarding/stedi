---
title: Polling
slug: legacy/sftp/polling
product: sftp
description: 'You can use Stedi SFTP to poll for new files. Here are some tips to keep the costs down.'
searchable: true
lifecycle: legacy
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

When you exchange files with your trading partner using SFTP, it’s common to poll for new files. There are costs you incur with polling, regardless of whether your trading partner does the polling or you do. We have a couple of tips to keep these costs down.

## Adjust the polling frequency

The main factor in determining the cost involved with polling, is how often your trading partner does it. The following table shows how their polling frequency influences your monthly costs, assuming a 31-day month.

| Polling frequency     | Costs per month<sup>*</sup> |
|-----------------------|-----------------------------|
| Once every 1 minute   |                       $6.70 |
| Once every 5 minutes  |                       $1.34 |
| Once every 60 minutes |                       $0.11 |

<sup>*</sup> These are the costs for the polling alone, without downloading any of the files. The actual costs may vary, as it depends on the number of files that are stored in SFTP. Which brings us to our next tip.

## Delete files you don’t need anymore

The typical scenario is that you make a file available containing a transaction and your trading partner downloads and processes it. You should tell your trading partner to delete the file once they’re done with it.

The costs vary based on the number of files, so deleting the files you don’t need anymore keeps the cost of polling down.

## Talk to your trading partner

Even though your trading partner is doing the polling, you incur the costs because the files are hosted in your Stedi account. You should talk to you trading partner and provide them with guidelines for using SFTP:

- Don’t poll more often than necessary.
- Only upload files that need to be processed.
- Only download files once.
- Delete files once they’re processed.

If your trading partner doesn’t follow these guidelines, you have the option to revoke their credentials and they will lose access to your SFTP. You can easily create new credentials for them once you’ve resolved the issue.
