---
title: Stedi Cloud - Build custom middleware
sidebarTitle: Overview
slug: cloud
description: "Customize and extend your EDI integraton with Stedi Cloud."
searchable: true
hidden: false
lifecycle: general_availability
childPageOrder:
  - cloud/mappings
  - cloud/functions
  - cloud/buckets
  - cloud/stash
---

[Stedi Core](/docs/core) allows you to configure EDI integrations without any custom development. Depending on your requirements, configuring Core may be the only thing you need to do to build an EDI integration on Stedi.

## Build custom middleware

Core produces EDI-like JSON that represents the structure of the original EDI transaction. Many integrations require transforming these JSON payloads into a structure that matches the data schemas for internal systems and business tools. Some integrations also require custom functionality.

You can use the following Stedi Cloud features to extend your EDI integration:

- **Mappings:** [Stedi Mappings](/docs/cloud/mappings) lets you transform the JSON output from Stedi Core into an internal shape or transform internal data into the JSON shapes required to generate EDI.
- **Functions:** [Stedi Functions](/docs/cloud/functions) allow you to run custom code on Stedi. You can use [event bindings](/docs/cloud/functions/event-bindings) to trigger functions automatically in response to Core events.
- **Buckets:** [Stedi Buckets](/docs/cloud/buckets) provides unlimited file storage.
- **Stash:** [Stedi Stash](/docs/cloud/stash) is a key-value store. You can use Stash to configure where you want to send data and even manage data feeds, like inventory or price catalogs.
