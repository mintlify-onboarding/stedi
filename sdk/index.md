---
title: Stedi SDK
slug: sdk
description: "Build with the Stedi SDK"
searchable: true
hidden: false
lifecycle: general_availability
---

Stedi provides a modular SDK that can be used to programmatically integrate with Stedi products. It is currently available for JavaScript and TypeScript.

|           |                                                                              |                                                                                                                                           |
|-----------|------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Core      | [`@stedi/sdk-client-partners`](/docs/sdk/clients/partners/docs/index.html)   | Generate EDI from JSON with Stedi. Details and examples are available in the [Core documentation](/docs/core/parsing-and-generating-edi). |
| Functions | [`@stedi/sdk-client-functions`](/docs/sdk/clients/functions/docs/index.html) | Manage and invoke Functions. Details and examples are available in the [Functions documentation](/docs/cloud/functions).                        |
| Mappings  | [`@stedi/sdk-client-mappings`](/docs/sdk/clients/mappings/docs/index.html)   | Map JSON documents from one structure to another. Details and examples are available in the [Functions documentation](/docs/cloud/mappings).                                                                                         |
| Buckets   | [`@stedi/sdk-client-buckets`](/docs/sdk/clients/buckets/docs/index.html)     | Manage buckets and upload or download files. Details and examples are available in the [Buckets documentation](/docs/cloud/buckets).                                                                                              |
| Stash     | [`@stedi/sdk-client-stash`](/docs/sdk/clients/stash/docs/index.html)         | Manage namespaces and manipulate key-value pairs. Details and examples are available in the [Stash documentation](/docs/cloud/stash).                                                                                         |
| SFTP      | [`@stedi/sdk-client-sftp`](/docs/sdk/clients/sftp/docs/index.html)           | Manage SFTP users. Details and examples are available in the [SFTP documentation](/docs/legacy/sftp).                                                                                                                        |
| AS2       | [`@stedi/sdk-client-as2`](/docs/sdk/clients/as2/docs/index.html)             | Manage AS2 server and partnerships. Details and examples are available in the [AS2 documentation](/docs/legacy/as2).                                                                                                       |
| Events    | [`@stedi/sdk-client-events`](/docs/sdk/clients/events/docs/index.html)       | Configure schedules and event delivery between products.                                                                                  |

## Authentication

You need a Stedi API Key to use the SDK. You can [create your first key using the UI](/docs/accounts-and-billing/authentication#creating-an-api-key). You can also use the [API Keys API](/docs/api/apikeys) to manage your keys.

### Passing the API key

You pass the API key when constructing a specific product client. The SDK uses this key to automatically secure all requests. The following example constructs a client and makes a request with an API key stored in an environment variable called `STEDI_API_KEY`.

```js
const client = new BucketsClient({
  region: "us",
  apiKey: process.env["STEDI_API_KEY"],
});

const result = await buckets.send(
  new ListObjectsCommand({ bucketName: "stedi-bucket" })
);
```
When deploying code that uses the Stedi SDK to [Functions](https://www.stedi.com/docs/cloud/functions), there is no need to specify an API key. The SDK will automatically make requests in the context of the current account.
