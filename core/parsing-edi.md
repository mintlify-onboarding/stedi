---
title: Parse EDI
slug: core/parsing-edi
sidebarTitle: "Parse EDI"
product: core
description: "Configure Stedi Core to ingest and parse EDI according to your partner's requirements."
searchable: true
hidden: false
lifecycle: general_availability
---

Core automatically parses and validates inbound EDI files in real-time. Once a file has been parsed, the [transaction data](/docs/core/transaction-data) can be reviewed manually or sent programmatically to an API endpoint.

## Configuration

To receive and parse EDI files, you must you must first [configure Core](/docs/core/configuration) for the trading partner. This includes specifying the transaction sets you plan to exchange with that partner and the communication methods you plan to use.

## Inbound processing flow

Core automatically processes all new files that arrive over a partnership's [connections](/docs/core/configuration/connections).

When a new EDI file arrives over a connection, Core automatically:

1. Confirms that the information in the file's `ISA` headers matches the corresponding partnership. If not, the file will error, and you can retry after creating the required configuration.
1. Identifies all available transaction sets in the file and validates them against the [transaction settings](/docs/core/configuration/transaction-settings) for the partnership, either using the specified guide or the base X12 specification.
1. Translates the EDI transaction into EDI-like JSON, using the JSON Schema defined by the guide.
1. Persists the original EDI file and the EDI-like JSON for review and retrieval. Files are retained for 10 years.
1. Displays the transaction set data in the UI for inspection.

## Send transactions to destinations

Core can deliver transactions and other events to any API endpoint using [destinations](/docs/events/destinations). For example, you can configure a destination that automatically sends inbound transactions to an API endpoint, or you can configure a destination that sends a notification to a ticketing system when a file fails to parse.

## Other file types

Core can also route non-EDI formats like CSV, JSON, and XML for further processing. When a non-EDI file arrives, the file will appear in the UI, and Core will emit an event that contains a summary of the file execution. This event can be used to trigger additional processing.

How Core processes non-EDI files depends on the file extension:

- Core will emit a [`file.processed` event](/docs/events/event-types#fileprocessed) for all files with `.json`, `.csv`, `.xml`, `.xls`, `.xlsx` or `.pdf` file extensions, which can be used to trigger additional processing.
- Core attempts to process any other file extension, such as .`txt` or `.edi`, as EDI. If parsing fails, Core attempts to parse the file first as JSON and then as CSV. If parsing still fails, Core marks the execution as failed and emits a [`file.failed` event](/docs/events/event-types#filefailed), which can be used to trigger additional processing.
