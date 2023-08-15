---
title: Functions - Run Custom Code
sidebarTitle: "Functions"
slug: cloud/functions
product: ""
description: "Functions product introduction"
searchable: true
lifecycle: general_availability
childPageOrder:
  - cloud/functions/creating-and-deploying
  - cloud/functions/invoking
  - cloud/functions/event-bindings
  - cloud/functions/error-handling
  - cloud/functions/limits
  - cloud/functions/sdk-reference
---

Functions is a compute service that lets you run code without managing servers. Stedi runs your code on high-availability compute infrastructure that automatically scales up and down according to your usage.

Functions offers the following benefits when building EDI integrations:

- Runs your code in a fully-managed hosted environment.
- Uses a long-term support version of the Node.js 18.x runtime.
- Allows you to use any third-party libraries.
- Integrates with other Stedi products such as [Core](/docs/core) and [Buckets](/docs/cloud/buckets) through event bindings.
- Supports asynchronous execution with built-in retries and an error queue per function.
- Unrestricted network access to integrate with Stedi products, your own APIs, or third-party services.
- Supports log retention and provides essential operational metrics per function.

## Use Cases

You use functions to build any type of integration or standalone backend service by supplying your source code. You may want to use functions for the following use cases:

- Respond to [Stedi Core events](/docs/events/event-types) to create an end-to-end EDI integration.
- Perform custom transformations and payload format translation in code. For example, you may want to convert JSON output from Core into XML, CSV, or IDoc formats for external business applications.
- Store transactional details in a database such as [Stash](/docs/cloud/stash) or save large payloads in a [Stedi Bucket](/docs/cloud/buckets).
- Integrate with internal or external API endpoints or webhooks.
- Wrap a potentially unreliable processing step with configurable retries and a built-in error queue.

## Programmatic access

You can use the [Functions SDK](/docs/sdk) or the [Stedi CLI](https://www.npmjs.com/package/@stedi/cli) to deploy and invoke functions. You can also use the [Functions HTTP API](/docs/functions/invoking#http-invoke-function-api) to call functions directly.

Visit [Creating and deploying functions](/docs/cloud/functions/creating-and-deploying) for instructions.
