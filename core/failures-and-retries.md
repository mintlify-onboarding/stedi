---
title: Failures and retries
slug: core/failures-and-retries
sidebarTitle: "Failures and retries"
product: core
description: "Learn possible Stedi Core processing failures for inbound and outbound transaction data. Manually retry files at any time to unblock your pipeline."
searchable: true
hidden: false
lifecycle: general_availability
---

When Core encounters EDI processing failures, it displays them in the **File Executions** tab and emits a [file.failed](/docs/events/event-types#filefailed) event.

## Inbound EDI

When Core ingests EDI, it must first match the data to a [partnership](/docs/core/configuration/profiles-and-partnerships) and then validate the incoming EDI according to the partnership configuration. Failures may occur when the file does not contain sufficient heading data to match transactions to an existing partnership or when the incoming file contains invalid EDI.

Core only marks an incoming file as successfully processed when all the individual transaction sets within the file are valid against either the guide or the base X12 specification. If one transaction set is invalid, Stedi flags the entire file as failed.

Other processing failures may occur occasionally, most commonly for missing configuration or misconfigured data. Failure causes include:

- Stedi cannot find a profile record matching the ISA headers.
- Stedi cannot find a partnership record defined for the two profiles detected.
- Stedi received a corrupt or unparseable file.

## Outbound EDI

Core uses the [Stedi guide](docs/core/configuration/transaction-settings#using-guides) and other partnership settings to generate valid EDI with the proper envelope and control numbers. Failures may occur when Core tries to deliver the file to a configured [connection](/docs/core/configuration/connections).

After generating EDI, Core marks the file as successfully processed after delivery to all configured connections. Core does not display transactions from within files that have failed delivery.

Core attempts to deliver a file to all configured connections up to three times before marking the file execution as `FAILED`. Core displays each delivery attempt, including failure details, in the UI and emits the [`file.failed`](/docs/events/event-types#filefailed) event after the third attempt.

## Manual retries

You can manually retry failed files on the **File executions** page in Core’s UI. To retry files, click **Retry**.

## Diagnosing failures

Core displays all of the events associated with each file execution and transaction in the UI. You can use this information to quickly debug issues and understand how your data flows through Core. Visit [Monitor transaction data](/docs/core/configuration/transaction-data#event-and-function-payloads) for details.
