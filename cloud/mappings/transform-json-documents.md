---
title: Transform JSON Documents
slug: cloud/mappings/transform-json-documents
product: ""
sidebarTitle: "Transform JSON Documents"
description: >-
  Use the Mappings API to transform JSON documents from one shape to another.
searchable: true
childPageOrder: []
---

After you create a [mapping definition](/docs/cloud/mappings/manage-mappings/mapping-definition) through the [Mappings UI](/docs/cloud/mappings/manage-mappings/ui-guide) or the [Mappings API](/docs/cloud/mappings/manage-mappings/api-guide), you can call the Mappings API to transform your JSON documents from one shape to another.

By default, the Mappings API doesn't validate incoming or transformed JSON. You can enable [**strict validation mode**](/docs/cloud/mappings/transform-json-documents#enable-payload-validation) to validate incoming and outgoing JSON payloads.

This page demonstrates how to use the Mappings API to transform JSON documents. Refer to the [Mappings API Reference](/docs/api/mappings) for a complete list of endpoints and possible error codes.

## Authentication

You need an API key to make calls to Stedi APIs. You can use an existing API key; you don't need a separate key for Mappings. You can [create a new API key](https://www.stedi.com/app/settings/api-keys) in your Stedi account.

Include the key in every call to the API as part of the `Authorization` header.

```http
Authorization: Key $YOUR_API_KEY
```

Refer to our [Authentication guide](/docs/accounts-and-billing/authentication) for more details.

## Mapping a single document

You can map a JSON document by POSTing it to `https://mappings.us.stedi.com/2021-06-01/mappings/mapping_id/map`. That URL includes the ID of the mapping definition you want to use, and the body of the request is the JSON you want to map.

```http
POST https://mappings.us.stedi.com/2021-06-01/mappings/01AB3DEF4GHIJ5KL67MNOP8QR9/map HTTP/1.1
Authorization: Key $YOUR_API_KEY
Content-Type: application/json

{
  "quantity": 15,
  "unit_price": 2.50
}
```

The body of the response is the mapped document.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "total": 37.50
}
```

## Mapping multiple documents

There is no endpoint that allows you to map multiple documents at once, so if you want to map multiple documents, you have to make a separate request for each document. This is easy enough to do for a small set of documents, but if you want to map, say, thousands of documents in a short time, you need to take a little bit more care.

Mappings allows you to do up to 200 concurrent requests, so if you need to map 200 documents or less, you can send them all at once. If you need to map more than 200 documents, you should make sure that you don't exceed 1000 requests per second. Also keep in mind that there's a maximum of one million requests per day.

If you make more requests than Mappings is able to handle, you will receive a 429 Too Many Requests response.

- Keep track of how many outstanding requests you have. Don't send any new requests if you have 200 or more.
- Keep track of how many total requests you've sent in a day and make sure it doesn't exceed 1,000,000.
- Keep a timestamp for every successful response you receive. When sending a new request, first check the timestamp for 1000 requests ago. If it's less than a second ago, wait until the second has passed before making a new request.
- If you receive a 429 Too Many Requests response, wait for one second before sending new requests. If you still get back 429 after waiting for one second, then wait for two seconds. If that's not enough wait for four, then eight, etc.
- If you receive a 429 Too Many Requests response, add the document back into the list of documents that you need to map. Don't resend the request immediately.

## Send a compressed payload to the API

To send a `gzip` compressed payload to the Mappings API, set the `Content-Encoding` header to the value of `gzip`. Mappings API currently only supports `gzip` compression.

Note that the [maximum document size limit][mappings-limits] **is applied after decompressing the content of the payload**. If the decompressed payload of a given request is larger than the maximum document size limit, the Mappings API will reject the request.

The following is an example written in [TypeScript][typescript] of creating mapping and sending a `gzip` compressed payload to the `/map` endpoint.

```ts
import axios from "axios";
import { gzipSync } from "zlib";

const API_KEY = "YOUR_API_KEY";
const rootURL = "https://mappings.us.stedi.com/2021-06-01/mappings";
const headers = { Authorization: `Key ${API_KEY}` };

async function doRequest() {
  const mapping = {
    name: "GzipPayloadMapping",
    mapping: `{"Hello": keyFromSource}`,
    type: "only_mapped_keys",
  };

  const createMappingResponse = await axios.post(rootURL, mapping, { headers });

  const payload = gzipSync(JSON.stringify({ keyFromSource: "World!" }));
  const mapResponse = await axios.post(`${rootURL}/${createMappingResponse.data.id}/map`, payload, {
    headers: {
      ...headers,
      "Content-Encoding": "gzip",
    },
  });

  console.log(mapResponse.data);
}

async function main() {
  try {
    await doRequest();
  } catch (e) {
    console.log(e);
  }
}

main();
```

## Enable payload validation

To validate the input and the output payload of the mapping operation,
set the `validation_mode` query parameter to `strict`.

Mappings uses the [source schema](/docs/cloud/mappings/manage-mappings/mapping-definition#source-schema)
to validate the input document and the [target schema](/docs/cloud/mappings/manage-mappings/mapping-definition#target-schema)
to validate the outgoing document.

The following example POST request maps a document and applies strict validation mode.

```http
POST https://mappings.us.stedi.com/2021-06-01/mappings/01AB3DEF4GHIJ5KL67MNOP8QR9/map?validation_mode=strict HTTP/1.1
Authorization: Key $YOUR_API_KEY
Content-Type: application/json

{
  "quantity": 15,
  "unit_price": 2.50
}
```

If the mapping is evaluated successfully and there are no validation failures, the body of the response is the mapped document.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "total": 37.50
}
```

If the evaluation of the mapping expression fails, then validation of the input and the output is not applied, the response is a 400 error with the `mapping_failed` code and no `validation_errors` object.

```http
HTTP/1.1 400
Content-Type: application/json

{
  "code": "mapping_failed",
  "message": "Failed to map input document: Key in object structure must evaluate to a string; got: ["1","2"] at position 1."
}
```

If validation of the input or output payload fails, the response is a 400 error with the `validation_failed` code and `validation_errors` object.

```http
HTTP/1.1 400
Content-Type: application/json

{
  "code": "validation_failed",
  "message": "The input of the mapping does not conform to the specified source JSON Schema, and the output of the mapping does not conform to the specified target JSON Schema.",
  "validation_errors": {
    "source": {
      "mapping_input": {
        "quantity": 15,
        "unit_price": 2.50
      },
      "errors": [
        {
          "code": "required",
          "message": "must have required property 'unit_description'",
          "json_pointer": "/",
          "json_schema_pointer": "#/required"
        }
      ]
    },
    "target": {
      "mapping_output": {
        "total": 37.50
      },
      "errors": [
        {
          "code": "type",
          "message": "must be integer",
          "json_pointer": "/total",
          "json_schema_pointer": "#/properties/total/type"
        }
      ]
    }
  }
}
```

If only the input or only the output has validation errors, the output is a partially-populated response object.

The following example shows a response when the input payload is invalid.

```http
HTTP/1.1 400
Content-Type: application/json

{
  "code": "validation_failed",
  "message": "The input of the mapping does not conform to the specified source JSON Schema.",
  "validation_errors": {
    "source": {
      "mapping_input": {
        "quantity": 15,
        "unit_price": 2.50
      },
      "errors": [
        {
          "code": "required",
          "message": "must have required property 'unit_description'",
          "json_pointer": "/",
          "json_schema_pointer": "#/required"
        }
      ]
    }
  }
}
```

The following example shows a response when the output payload is invalid.

```http
HTTP/1.1 400
Content-Type: application/json

{
  "code": "validation_failed",
  "message": "Invalid request: The output of the mapping does not conform to the specified target JSON Schema.",
  "validation_errors": {
    "target": {
      "mapping_output": {
        "total": 37.50
      },
      "errors": [
        {
          "code": "type",
          "message": "must be integer",
          "json_pointer": "/total",
          "json_schema_pointer": "#/properties/total/type"
        }
      ]
    }
  }
}
```

[mappings-limits]: https://www.stedi.com/docs/limits#mappings
[typescript]: https://www.typescriptlang.org/
