---
title: Monitor transactions
slug: core/transaction-data
sidebarTitle: "Monitor transactions"
product: core
description: "Monitor, inspect, test, and debug the business data flowing through Stedi Core. Core translates data between EDI and JSON and provides complete visibility into all of your transactions."
searchable: true
hidden: false
lifecycle: general_availability
---

You can monitor, inspect, test, and debug the transaction data flowing through Core.

## Filter and sort

You can review all incoming and outgoing transaction data using [**File executions**](https://www.stedi.com/app/core/file-executions) and [**Transactions**](https://www.stedi.com/app/core/transactions).

### File executions

Each file sent or received can contain multiple transactions. [**File executions**](https://www.stedi.com/app/core/file-executions) shows every file you have exchanged with trading partners, its processing status, and the number of transactions within the file.

You can filter file executions by various attributes, including status (such as `Failed` or `Completed`), direction, partnership, and fault (such as `failed to find guide`).

### Transactions

Individual [**Transactions**](https://www.stedi.com/app/core/transactions) only appear after an entire file has been successfully processed. Every transaction that has been successfully sent to or received from your trading partners appears in this list.

You can filter transactions by various attributes, including transaction set, sender, receiver, direction, and usage (such as `production` or `test`). You can also search for specific transaction sets based on a business identifier, such as a purchase order number.

## Inspect raw data

You can review the raw EDI or JSON data for processed files and individual transactions. Click the name of the file or transaction to review its data.

### View a file execution

The file execution detail page contains processing results and the entire source of the original, unmodified file. You can copy or download the source file to your local machine.

### View a transaction

The transaction detail page includes two tabs that display different levels of information.

- **Details** displays metadata about the transaction, including the sender, receiver, the guide used for validation, and more.
- **Inspector** shows the transaction payload in a human-friendly view, which you can use to understand the contents of the transaction.

You can also view and download the raw EDI or JSON data for a transaction.

> **Note for advanced EDI users:** When viewing the raw EDI for a single transaction, the transaction EDI view includes the ISA (Interchange Control Header) and GS (Functional Group Header) from the source file. However, it does not include any other transaction sets that were in the source file, so the segment counts may not total correctly.

## Manage failed file executions

You can review the processing status for all incoming and outgoing files on the **File executions** page.

You can individually retry or ignore failed file executions. You can also bulk retry or ignore multiple file executions at once.

Typically, you would retry a file execution if you have resolved the issue that caused the failure. For example, if a file failed because it was missing a required segment, you could retry the file execution after marking the segment as optional in the guide. You would ignore a file execution if you do not want to reprocess the file. For example, if you inputted the failed file to your system manually, you could ignore the file execution to prevent it from being processed again.

### Bulk processing

Before you initiate a bulk retry or ignore, you can filter the list of file executions to only show the files you want to take action on. For example, you could filter the list to show only files with a certain fault reason, and then bulk retry or ignore all of those files.

- To bulk retry or ignore, click **Retry** or **Ignore**. After a confirmation step, this will trigger a retry or ignore for every file with the `Failed` status that matches your selected filters.
