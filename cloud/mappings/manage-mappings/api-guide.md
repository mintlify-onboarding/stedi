---
title: Manage mapping definitions with the API
slug: cloud/mappings/manage-mappings/api-guide
product: ""
sidebarTitle: "Use Mappings API"
description: >-
  Use Mappings API to create and manage mapping definitions.
searchable: true
childPageOrder: []
---

This page explains how to use the Mappings API to create, read, update, and delete Stedi mapping definitions.

- Refer to the [Mappings API Reference](/docs/api/mappings) for a complete list of endpoints and possible error codes.
- You can also do these actions in the [Mappings UI](/docs/cloud/mappings/manage-mappings/ui-guide).

Before you begin, we recommend reviewing the [Mapping Definition Overview](/docs/cloud/mappings/manage-mappings/mapping-definition).

## Authentication

You need an API key to make calls to Stedi APIs. You can use an existing API key; you don't need a separate key for Mappings. You can [create a new API key](https://www.stedi.com/app/settings/api-keys) in your Stedi account.

Include the key in every call to the API as part of the `Authorization` header.

```http
Authorization: Key $YOUR_API_KEY
```

Refer to our [Authentication guide](/docs/accounts-and-billing/authentication) for more details.

## Create a mapping definition

You create a mapping definition by POSTing to `https://mappings.us.stedi.com/2021-06-01/mappings`. At the very least, your mapping definition should have a name, mapping type, and a few mapping expressions.

```http
POST https://mappings.us.stedi.com/2021-06-01/mappings HTTP/1.1
Authorization: Key $YOUR_API_KEY
Content-Type: application/json

{
  "name": "A minimal example",
  "type": "only_mapped_keys",
  "mapping": "{ \"total\": item.quantity * item.unit_price }"
}
```

The mapping expressions look like JSON, but they're not. JSONata has all kinds of syntax that isn't valid JSON. That's why you need to provide the mapping expressions inside of a string.

### Include a target example

If you want to use the mapping type _Merge with target example_, then you need to provide a target example. You do this by including a target schema that contains the target example. The following mapping definition provides a default value of `0` for the output field `total`.

```http
POST https://mappings.us.stedi.com/2021-06-01/mappings HTTP/1.1
Authorization: Key $YOUR_API_KEY
Content-Type: application/json

{
  "name": "A target example example",
  "type": "merge_with_target_example",
  "mapping": "{ \"total\": item.quantity * item.unit_price }",
  "target": {
    "name": "target.json",
    "type": "jsonschema@2020-12",
    "content": "{\"$schema\": \"https://json-schema.org/draft/2020-12/schema\", \"default\": {\"total\": 0 }}"
  }
}
```

The target schema needs to be passed inside a string. That makes the above example a bit hard to read, so here's the target schema with more sane formatting.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "default": {
    "total": 0
  }
}
```

### Include a lookup table

You can add one or more lookup tables by providing the `lookup_tables` field. Every lookup table must have a name and a set of values.

```http
POST https://mappings.us.stedi.com/2021-06-01/mappings HTTP/1.1
Authorization: Key $YOUR_API_KEY
Content-Type: application/json

{
  "name": "A lookup table example",
  "type": "only_mapped_keys",
  "mapping": "{ \"country\": $lookupTable($tables.countries, \"german\", land).english }",
  "lookup_tables": [
    {
      "name": "countries",
      "values": [
        {
          "english": "United States",
          "spanish": "Estados Unidos",
          "german": "Vereinigte Staaten"
        },
        {
          "english": "Mexico",
          "spanish": "México",
          "german": "Mexiko"
        },
        {
          "english": "Germany",
          "spanish": "Alemania",
          "german": "Deutschland"
        }
      ]
    }
  ]
}
```

### Response

The response contains a copy of the mapping definition you just created, plus a couple of extra fields.

| Field        | Description                                                                                                                                           |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `id`         | The unique identifier for your mapping definition. You need this if you ever want to update, delete, or use your mapping definition.                  |
| `created_at` | A timestamp indicating when you created this mapping definition.                                                                                      |
| `updated_at` | A timestamp indicating when you last updated this mapping definition. If you've never updated the mapping definition, this is the same as created_at. |

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": "01AB3DEF4GHIJ5KL67MNOP8QR9",
  "name": "A minimal example",
  "type": "only_mapped_keys",
  "mapping": "{ \"total\": item.quantity * item.unit_price }"
  "created_at": "2022-01-01T20:22:01.01Z",
  "updated_at": "2022-01-01T20:22:01.01Z"
}
```

## Retrieve a mapping definition

You retrieve a mapping definition by GETting it from `https://mappings.us.stedi.com/2021-06-01/mappings/mapping_id`. The response is identical to the one you received when you created the mapping definition. In other words, it's the mapping definition as you specified it, supplemented with the fields `id`, `created_at`, and `updated_at`.

## Update a mapping definition

You update a mapping definition by PUTting a new version at `https://mappings.us.stedi.com/2021-06-01/mappings/mapping_id`. You need to specify an entire new mapping definition, so when we talk about updating the mapping definition, we really mean replacing it.

Other than that, updating a mapping definition is the same as creating one: the request body is the same, the response body is the same.

## Delete a mapping definition

You delete a mapping definition by DELETEing it at `https://mappings.us.stedi.com/2021-06-01/mappings/mapping_id`. That's all there is to it, really. You don't need to provide a request body and the response will be empty as well.

## List mapping definitions

You retrieve a list of all your mapping definitions by GETting it from `https://mappings.us.stedi.com/2021-06-01/mappings`.

```http
GET https://mappings.us.stedi.com/2021-06-01/mappings HTTP/1.1
Authorization: Key $YOUR_API_KEY
```

The response has a field `mappings` which contains a list with mapping definitions.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "mappings": [
    {
      "id": "01AB3DEF4GHIJ5KL67MNOP8QR9",
      "name": "A minimal example",
      "type": "only_mapped_keys",
      "created_at": "2022-01-01T20:22:01.01Z",
      "updated_at": "2022-01-01T20:22:01.01Z"
    },
    {
      "id": "02JK3LMN4OPQR5ST67UVWX8YZ9",
      "name": "A target example example",
      "type": "merge_with_target_example",
      "created_at": "2022-01-01T20:22:01.01Z",
      "updated_at": "2022-01-01T20:22:01.01Z"
    }
  ]
}
```

### Metadata

The list in the response doesn't contain complete mapping definitions; only metadata. It includes the fields `id`, `name`, `type`, `created_at`, and `updated_at`, but not the mapping expressions or the schema. If you're only displaying a list of mapping definitions, this is typically what you want, but should you need the full mapping definitions, you can set the query parameter `metadata_only` to `false`.

```http
GET https://mappings.us.stedi.com/2021-06-01/mappings?metadata_only=false HTTP/1.1
Authorization: Key $YOUR_API_KEY
```

The response will now include the mapping expressions and the schemas.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "mappings": [
    {
      "id": "01AB3DEF4GHIJ5KL67MNOP8QR9",
      "name": "A minimal example",
      "type": "only_mapped_keys",
      "mapping": "{ \"total\": item.quantity * item.unit_price }"
      "created_at": "2022-01-01T20:22:01.01Z",
      "updated_at": "2022-01-01T20:22:01.01Z"
    },
    {
      "id": "02JK3LMN4OPQR5ST67UVWX8YZ9",
      "name": "A target example example",
      "type": "merge_with_target_example",
      "mapping": "{ \"total\": item.quantity * item.unit_price }",
      "target": {
        "name": "target.json",
        "type": "jsonschema@2020-12",
        "content": "{\"$schema\": \"https://json-schema.org/draft/2020-12/schema\", \"default\": {\"total\": 0 }}"
      },
      "created_at": "2022-01-01T20:22:01.01Z",
      "updated_at": "2022-01-01T20:22:01.01Z"
    }
  ]
}
```

### Paging

If you have a lot of mapping definitions, you may not receive them all in one response. In that case, the response will contain a field `next_page_token`.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "mappings": [
    ...
  ],
  "next_page_token": "0t1H23ER4e5ArEMORE6REsU78l_TSyET"
}
```

You can make another request passing the value of that field to the query parameter `page_token` and you will receive the next few mapping definitions.

```http
GET https://mappings.us.stedi.com/2021-06-01/mappings?page_token=0t1H23ER4e5ArEMORE6REsU78l_TSyET HTTP/1.1
Authorization: Key $YOUR_API_KEY
```

As long as the field `next_page_token` shows up in the response, there are more mapping definitions to retrieve.
