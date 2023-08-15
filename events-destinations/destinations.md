---
title: Destinations - Send data to HTTPS endpoints
sidebarTitle: "Send data to destinations"
slug: events/destinations
description: "Stedi Destinations allow you to send events from Stedi to HTTP endpoints with fine-grained control and no custom code."
searchable: true
childPageOrder: []
---

Destinations allow you to send events from Stedi to third-party services without writing any custom code. You can use destinations to:

- Send translated EDI data from Stedi Core to external APIs and business applications
- Forward error events to Slack or other alerting applications
- Send all events to an external system to run custom business logic

Destinations are configured to run based on event binding rules, which will match events emitted by Stedi Core and Buckets (Cloud only). The destination will then make an API request to forward the event to the secured HTTP endpoint. Event Destinations support several authentication strategies, and include a queue for catching errors, so failed deliveries can be retried later.

## Limitations

Delivery success is based on the HTTP response. If you're sending to an API that has a non-standard response, we suggest instead using functions for full control.

Destinations do not directly transmit EDI data (for example on a `transaction.processed`), but instead show where to fetch it from. To get it, you need to use the [Buckets SDK](https://www.stedi.com/docs/sdk), which runs in a Node.js environment. We do have options for other environments, please reach out.

## Creating destinations

To use destinations you must first make an auth, then a destination, and an event binding.

- The auth defines how to authenticate with a specific API.
- The destination defines the exact endpoint,
- The event bindings define what events should be forwarded to the destination.

To create a new destination, go to **Core > Events > Destinations**.

### Auths

A single authentication can be used across multiple event destinations. There are a few different types:

| Type       | Description                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------- |
| API Keys   | The API keys as headers in the request. The most common version is ‘bearer tokens’.                                             |
| Basic Auth | [HTTP Basic Auth](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication), where you provide a username and password. |

#### Unauthenticated endpoints

There isn't a 'no-auth' option with Event Destinations, but it can be easily simulated with an API Keys auth. Just set the headers to a random value, and they will be ignored by the third-party service.

### Destinations

The destination simply defines what endpoint the event should go to. All you need to define is the endpoint's URL, and what HTTP method to use (POST, PUT, GET, etc).

**Rate Limit**. Use this to avoid overloading the third-party service. It limits the amount of requests that are made per second.

### Event Bindings

Event bindings allow you to determine what Stedi events should trigger the destination. For example, you might want to send data to the endpoint every time Core emits a new [`transaction.processed` event](/docs/events/event-types#transactionprocessed) A single destination can have multiple event bindings.

## Error Handling

### HTTP Response Codes

Failures are interpreted by the Destination's HTTP response code. Events associated with error code 429 and 5xx are retried. Events associated with error codes 1xx, 2xx, 3xx, and 4xx (excluding 429) aren't retried.

If you’re working with an API that doesn’t follow HTTP error code standards, we recommend using [Functions](/docs/cloud/functions) to integrate instead.

### Response Time

The endpoint you're sending to must respond within 5 seconds, or the event will be counted as a failed delivery. Similarly, if you're working with a slow API, we recommend using [Functions](/docs/cloud/functions) to send events instead.

### Error Queue

To keep track of failed deliveries, each destination includes an error queue. This ensures if the target service has some downtime, or anything else goes wrong, the missed events can be retried later. Each item in the queue consists of the original event that was attempted to be delivered. Viewing the HTTP response from the third-party service isn't possible, if you're working with a particularly tricky API, [Functions](/docs/cloud/functions) may be more appropriate.

The order of the error queue isn't guaranteed. The downstream service must be designed to be idempotent - where the order events are consumed doesn't matter.
