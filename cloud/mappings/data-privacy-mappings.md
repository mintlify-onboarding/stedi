---
title: Mappings Data and Privacy
slug: cloud/mappings/faqs
sidebarTitle: "Data and Privacy"
product: ''
description: ''

searchable: true
childPageOrder: []
---
Below is a list of frequently asked questions about the Mappings API. If you have any questions not listed here, please [contact us](https://www.stedi.com/contact).

## Data & Privacy

### How does the Mappings API use my data?

In short, we don’t. The payloads sent to or from the `/map` endpoint are not stored – temporarily or permanently. The payload is encrypted in transit.

### Is data sent to the Mappings API stored?

The `/map` endpoint is a stateless system that executes a transformation on your data. The transformation happens during the duration of the request; we do not retain the payload once a response is returned.

### What does Stedi log when I hit the Mappings API?

We log only request metadata such as the size of the request, latency, the account and mapping IDs.

These data points allow our engineers to monitor the health of the system and drive improvements to the service.
