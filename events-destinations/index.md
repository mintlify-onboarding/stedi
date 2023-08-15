---
title: Events
sidebarTitle: Events
slug: events
product: destinations
description: "Destinations allow you to forward events from Stedi to external APIs with fine-grained control and no custom code."
searchable: true
hidden: false
lifecycle: general_availability
childPageOrder:
  - events/event-types
  - events/destinations
---

Core emits several [event types](/docs/events/event-types) as it detects and transforms files.

## View Core events

Core displays all of the events associated with each file execution and transaction in the UI. You can use this information to quickly debug issues and understand how your data is flowing through Core. Visit [Manage transaction data](/docs/core/transaction-data#event-and-function-payloads) for details.

## Forward Core events and data

You can create [destinations](/docs/events/destinations) that automatically forward these events to secured HTTP endpoints.

## Consume events with Stedi Functions (Stedi Cloud)

[Stedi Functions](/docs/cloud/functions) is a compute service in Stedi Cloud that lets you run code without managing servers. Stedi runs your code on high-availability compute infrastructure that automatically scales up and down according to your usage.

You can use Stedi functions to consume events from Core. You can subscribe to events using [event bindings](/docs/cloud/functions/event-bindings) to select the exact subset of events you want to process.

Your function can then use JavaScript or TypeScript code to process the data, integrating with your business systems as needed. For example, you may want to generate an email alert when a file fails to validate or perform other custom actions.
