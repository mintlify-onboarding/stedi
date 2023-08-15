---
title: Mapping Definition Overview
slug: cloud/mappings/manage-mappings/mapping-definition
sidebarTitle: "Mapping Definition"
product: ""
description: >-
  A mapping definition describes how to turn input JSON into output JSON. It consists of mapping expressions, a mapping type, a target example, a set of schemas, and lookup tables.
searchable: true
---

A mapping definition describes how to turn input JSON into output JSON. It consists of several parts.

- A set of mapping expressions, describing how to transform input fields to output fields
- A mapping type, specifying how output fields are determined
- A target example, providing default values for output fields
- A set of schemas, describing what the input and output JSON should look like
- Lookup tables, to make converting values in mapping expressions easier

You can create mapping definitions with either the [Mappings UI](/docs/cloud/mappings/manage-mappings/ui-guide) or the [Mappings API](/docs/cloud/mappings/manage-mappings/api-guide).

## Mapping expressions

A mapping expression is a piece of code that specifies how to turn input fields into an output field. The following example shows how you could output a total price from a quantity and a unit price.

```jsonata
{ "total": item.quantity * item.unit_price }
```

On the left is the name of the output field, on the right is the mapping expression, written in [JSONata](https://docs.jsonata.org/overview.html). You can add more output fields, separated by commas.

```jsonata
{ "total": item.quantity * item.unit_price, "currency": item.currency, "product": product.id }
```

The result looks just like JSON, even though it technically isn't, because of the syntax of JSONata.

> Refer to [Write Mapping Expressions](/docs/cloud/mappings/jsonata) for full details, examples, and JSONata resources.

## Mapping types

All mapping types produce output fields based on the mapping expressions.

The mapping type specifies how the Mappings API generates the output field when the mapping expression doesn't produce a value. A mapping expression may not produce a value when one or more of the input fields that the mapping expression depends on aren't present.

### Only mapped keys

By default, Mappings adds the target keys you selected to the output and nothing else.

The mappings API handles output fields in the following ways:

- Adds an output field for every mapping expression that returns a result
- Does not add output fields based on the target example
- Does not add output fields based on the input.

Consider the following mapping expression.

```jsonata
{ "currency": item.currency, "total": item.quantity * item.unit_price }
```

You provide the following input.

```json
{
  "item": {
    "unit_price": 2.5,
    "currency": "USD"
  }
}
```

The Mappings API produces the following output.

- The field `currency` is added to the output because there is a mapping expression for `currency` that produces a result.
- The field `total` is not added to the output. There is a mapping expression for `total`, but it doesn't produce a result because `item.quantity` is missing from the input.
- The fields `item.unit_price` and `item.currency` are not added to the output, because there are no mapping expressions for them.

```json
{
  "currency": 5
}
```

### Merge with target example

If you need to produce JSON based on a template, and you only need to change a handful of fields, it's easier if you copy the entire target to the output and only make those few changes. For example, you may want to use a default currency for your output.

The mappings API handles output fields in the following ways:

- Adds an output field for every mapping expression that returns a result
- Copies an output field from the target example, unless that output field was already added because of a mapping expression.
- Does not add output fields based on the input.

Consider the following mapping expressions.

```jsonata
{ "unit_price": item.price, "quantity": item.quantity, "total": item.quantity * item.price }
```

You use these expressions with the following target example.

```json
{
  "currency": "USD",
  "quantity": 1,
  "unit_price": 0
}
```

You provide the following input.

```json
{
  "item": {
    "price": 2.5
  }
}
```

The Mappings API produces the following output.

- The field `currency` is copied from the target example because there is no mapping expression for `currency`.
- The field `unit_price` is not copied from the target example, because there is a mapping expression for `unit_price` that produces a result.
- The field `quantity` is copied from the target example. There is a mapping expression for `quantity`, but that one doesn't produce a result, because `item.quantity` is missing from the input.
- The field `total` doesn't end up in the output. There's a mapping expression for `total`, but it doesn't produce a result and the target example doesn't contain a default value for `total`.

```json
{
  "currency": "USD",
  "unit_price": 2.5,
  "quantity": 1
}
```

### Pass through

If your output needs to be much like your input, but with a few values changed, then you can tell Mappings to start with a copy of your input.

The mappings API handles output fields in the following ways:

- An output field will be added for every mapping expression that returns a result.
- An output field will be copied from the input, unless that output field was already added because of a mapping expression.
- No output fields will be added based on the target example.

Consider the following mapping expressions.

```jsonata
{
  "item": { "quantity": item.quantity * items_per_unit },
  "tax_rate": tax_rate < 1 ? tax_rate + 1 : tax_rate,
  "total": item.quantity * item.price
}
```

You provide the following input.

```json
{
  "item": {
    "quantity": 5,
    "price": 2.5,
    "currency": "USD"
  },
  "tax_rate": 0.06
}
```

The Mappings API produces the following output.

- All fields are copied from the input.
- The field `tax_rate` is overwritten because there is a mapping expressions for `tax-rate` that produces a result.
- The field `item.quantity` is not overwritten. There is a mapping expressions for `item.quantity` , but it doesn't produce a result, because `items_per_unit` is missing from the input.
- The field `total` is added, because there's a mapping expression for `total` that produces a result.

```json
{
  "item": {
    "quantity": 5,
    "price": 2.5,
    "currency": "USD"
  },
  "tax_rate": 1.06,
  "total": 12.5
}
```

## Target example

The target example is a JSON object that provides default values in case a mapping expression doesn't return a result. For example, the following target example sets the default currency to `USD`.

```json
{
  "default": {
    "currency": "USD"
  }
}
```

If the mapping expression can't determine the currency—for example because the input document doesn't contain currency information—then it uses the default from the target example. For this to work, the mapping type must be set to _Merge with target example_. Any other mapping type completely ignores the target example. Also, if you set the mapping type to _Merge with target example_, you must provide a target example. It can be empty, but it must be part of the mapping definition.

The target example is part of the target schema.

## Schemas

The Mappings UI uses the source and target schemas are used by the Mappings UI. If you don't plan on using the Mappings UI—i.e. you'll use the API exclusively—then you generally don't need to provide schemas. The only exception is when you want to provide [default values for your output fields](#target-schema).

Any schema you specify must be a valid [JSON Schema](http://json-schema.org/understanding-json-schema/) (version 2020-12). Also, each schema needs to be self-contained, so you can't include references to external schemas.

### Source schema

The Mappings UI uses the source schema to show an example of an input document. You can use this example to find and select the input fields you need in your mapping expressions. The following schema specifies three input fields: `quantity` , `unit_price` and `currency`.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "quantity": {
      "type": "number"
    },
    "unit_price": {
      "type": "number"
    },
    "currency": {
      "type": "string",
      "minLength": 3,
      "maxLength": 3
    }
  }
}
```

Just the schema isn't enough. You also need to provide some values for those fields. The Mappings UI uses the values to show what the output of a mapping expression will be like while you are crafting that expression, which is a useful feature. You can add values to the example by extending the source schema with a default document.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "quantity": {
      "type": "number"
    },
    "unit_price": {
      "type": "number"
    },
    "currency": {
      "type": "string",
      "minLength": 3,
      "maxLength": 3
    }
  },
  "default": {
    "quantity": 15,
    "unit_price": 2.5,
    "currency": "CAD"
  }
}
```

### Target schema

The Mappings UI uses the target schema to show an example of an output document. You can only create mapping expressions for fields that are included in the target schema, so without a target schema, you can't use the Mappings UI. The following schema specifies two output fields: `total` and `currency`.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "total": {
      "type": "number"
    },
    "currency": {
      "type": "string",
      "minLength": 3,
      "maxLength": 3
    }
  }
}
```

As with the source schema, you can add a default document to your target schema. Unlike with the source schema, this doesn't help with writing mapping expressions. However, if you want to provide default values for your output fields, adding a default document to your target schema is the way to do that. The following example sets the default currency to `USD`.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "properties": {
    "total": {
      "type": "number"
    },
    "currency": {
      "type": "string",
      "minLength": 3,
      "maxLength": 3
    }
  },
  "default": {
    "currency": "USD"
  }
}
```

Default values only work if you set the mapping type to _Merge with target example_.

## Lookup tables

A lookup table helps the Mappings API convert a value from one representation to another. For example, the following table allows you to translate country names.

| english       | spanish        | german             |
| ------------- | -------------- | ------------------ |
| United States | Estados Unidos | Vereinigte Staaten |
| Mexico        | México         | Mexiko             |
| Germany       | Alemania       | Deutschland        |

There's no primary key in a lookup table. You can freely translate from English to Spanish, from Spanish to German, from German to English; any direction you want. You specify this in the mapping expression using the `$lookupTable` function. The following example uses the lookup table called `countries` to translate the input field `land` (which is German for _country_) from German to English.

```
{
  "country": $lookupTable($tables.countries, "german", land).english
}
```

The following example shows the same `countries` lookup table in JSON.

```json
[
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
```
