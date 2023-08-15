---
title: Event Bindings
sidebarTitle: "Event bindings"
slug: cloud/functions/event-bindings
product: ""
description: ""
searchable: true
childPageOrder: []
---

Event bindings and functions allow you to integrate Stedi with external systems and applications.

Event bindings automatically invoke your function asynchronously in response to [Stedi Core events](/docs/events/event-types). For example, you could invoke a function when Core successfully translates an EDI file from a specific trading partner.

# Prerequisites

Before creating a binding, complete the following setup:

- [Configure Stedi Core](/docs/core/configuration/profiles-and-partnerships) for at least one trading partner and ensure Core can successfully process inbound files.
- [Create and deploy a function](/docs/cloud/functions/creating-and-deploying) where you want to add an event binding.

To test events and event bindings, you can deploy the following simple function that logs the Core events it receives.

```javascript
export const handler = async (event, context) => {
  console.dir(event, { depth: null });
};
```

# Create an event binding in the UI

To configure an event binding:

1. [In Functions UI](/app/functions), open your function.
1. On the **Overview** tab, click the **Add Event Binding** button to open the binding editor.
1. In the binding editor, you can:
   - Subscribe to one of the default **Detail Types**, like [`transaction.processed`](/docs/events/event-types#transactionprocessed), by selecting it from the list.
   - Create a custom binding. A custom binding allows you to filter a specific subset of events using a custom event pattern. Visit [Creating custom event patterns](#custom-event-patterns) for details.
1. Click **Create**.

Your event binding will take a minute or so to create. It's ready once the status has changed from **Under change** to **Available**. Drop an EDI file into Core to try it out.

# Events reference

Stedi emits the following events.

- `transaction.processed`
- `functional_group.processed`
- `file.created`
- `file.processed`
- `file.delivered`
- `file.failed`

Visit [Events](/docs/events/event-types#available-events) in the Core documentation for complete details and full event structures.

# Custom event patterns

Event patterns are simple JSON objects that have the same structure as the events that they match.

You only need to include the fields in your event pattern that you want to match on, and the values must always be an array. This approach allows a single pattern to match many different events.

The following example pattern would match any `file.processed` event when `type` is “CSV” .

**Example event pattern**

```json
{
  "detail-type": ["file.processed"],
  "detail": {
    "source": {
      "type": ["CSV"]
    }
  }
}
```

**Example matching `file.processed` event for an incoming CSV**

```json
{
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "file.processed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
	"metadata": {
  		"processedAt": "2023-03-14T19:19:17.950Z"
	},
	"source": {
  	"type": "CSV",
    "bucketName": "test-bucket",
    "key": "123ABC/1746-1746-1.txt",
    "size": 4301
  }
}
```

# A complete EDI to Webhook example

The following example shows a function designed to consume an [`transaction.processed`](/docs/events/event-types#transactionprocessed) event from Core. It retrieves the translated JSON and delivers that translated JSON to a HTTP endpoint.

```javascript
const s3 = require("@aws-sdk/client-s3");

exports.handler = async function (event, context) {
  // get bucket reference for the JSON version of EDI Transaction Set
  const { output } = event;

  // retrieve the file contents using bucket reference
  const client = new s3.S3Client({});
  const getFile = await client.send(new s3.GetObjectCommand({ Bucket: output.bucketName, Key: output.key }));
  const raw = await getFile.Body.transformToString();
  const ediAsJson = JSON.parse(raw);

  // send JSON to endpoint
  const result = await fetch("http://example.com/", {
    method: "POST",
    body: ediAsJSon,
  });

  return { ok: result.ok, statusCode: result.status, ediAsJson };
};
```
