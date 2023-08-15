---
title: Mappings - Transform JSON shapes
sidebarTitle: "Mappings"
slug: cloud/mappings
product: mappings
description: >-
  Automated data mapping that integrates with any pipeline. Build a mapping
  definition quickly using uncluttered UI and JSONata mapping expressions. Use
  the API to turn JSON into whatever shape you want.
searchable: true
childPageOrder:
  - cloud/mappings/manage-mappings
  - cloud/mappings/jsonata
  - cloud/mappings/transform-json-documents
  - cloud/mappings/data-privacy-mappings
---

Stedi mappings transform JSON documents from one structure to another. For example, you could create a mapping to transform incoming JSON between the following shapes.

**Source shape**

```json
{
  "product": {
    "id": "QL-5490S",
    "price": "USD 500"
  }
}
```

**Target shape**

```json
{
  "product_number": "QL-5490S",
  "price": {
    "currency": "USD",
    "amount": 500
  }
}
```

First, you must create a [mapping definition](/docs/cloud/mappings/manage-mappings/mapping-definition) with the [Mappings UI](/docs/cloud/mappings/manage-mappings/ui-guide) or the [Mappings API](/docs/cloud/mappings/manage-mappings/api-guide). Then, you can use that definition to [transform JSON documents](/docs/cloud/mappings/transform-json-documents) with the Mappings API.

# Use Cases

You may want to use mappings for one or more of the following use cases:

- Transform into or out of EDI-like JSON when using [Stedi Core](/docs/core)
- An API expects data in a structure that's different than what your own software system uses
- Map data from one transaction set, like a purchase order, directly onto another transaction set, like an invoice

## Transform EDI-like JSON

[Stedi Core](/docs/core) transforms data between EDI and JSON. However, Core expects and outputs EDI-like JSON, a JSON shape that represents an EDI document.

You can use mappings in conjunction with Core to transform EDI data into and out of the shape required for your internal systems. In this case, we recommend [creating a mapping based on the Stedi guide](/docs/core/guides/create-a-mapping-from-a-stedi-guide) that Core uses to read or write EDI.
