---
title: FAQs
slug: legacy/edi-core/faqs
product: ""
description: ""
searchable: false
childPageOrder: []
---

# Frequently asked questions

Below is a list of frequently asked questions about the EDI Core API. If you have any questions not listed here, please [contact us](https://www.stedi.com/contact).

## Data & Privacy

### How does the EDI Core API use my data?

In short, we don’t. The payloads sent to the `translate` API are not stored – temporarily or permanently. The contents of your file are encrypted in transit.

### Is data sent to the EDI Core API stored?

The `translate` API is a stateless system that executes a transformation on your data. The transformation happens during the duration of the request; we do not retain the payload once a response is returned.

### What does Stedi log when I hit the EDI Core API?

We log only request meta information such as the size of the request, latency, transaction sets, and release.

These data points allow our engineers to monitor the health of the system and drive improvements to the service. They help the team answer questions like:

- How many requests did the API receive over the past week?
- What percentage of the responses returned a 4XX/5XX status code?
- Why did latency increase after the most recent deployment?

If a request fails with a 500 status code, an engineer on the team is notified (yes, we investigate every single one).
