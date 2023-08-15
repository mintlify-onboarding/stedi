---
title: JSONata Cheatsheet
slug: cloud/mappings/jsonata/jsonata-cheatsheet
product: ""
description: ""
searchable: true
childPageOrder: []
---

All mappings expressions are based on [JSONata language](http://docs.jsonata.org/overview.html) - this page showcases its most important features useful when creating a mapping.

## 1. JSON object source document

The source for each example is derived from the following JSON:

```json
{
  "senderName": "STEDI",
  "customerID": "997321",
  "shipmentID": 3312412,
  "shipment type": "ASAP",
  "address": {
    "street": "1234 Main St.",
    "city": "Los Angeles",
    "state": "CA",
    "country name": "USA",
    "is-europe": false
  },
  "orders": [
    {
      "orderDate": "2021/03/17",
      "productID": "DEG32",
      "quantity": 3,
      "pricePerUnit": 5,
      "volume": "10"
    },
    {
      "orderDate": "2021/10/12",
      "productID": "OIU98",
      "quantity": 100,
      "pricePerUnit": 1,
      "volume": "15"
    },
    {
      "orderDate": "2021/01/07",
      "productID": "PWE47",
      "quantity": 45,
      "pricePerUnit": 500,
      "volume": "35"
    }
  ]
}
```

### 1.1 Retrieving data

#### Root-level field

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/HethHJX?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Nested field in the root object

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/miLviOe?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Nested field with a dash in its name in the root object

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/WGkCK6O?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Nested field in a root level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/Q0Uvq0N?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve an array of items from a root level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="485" data-src="https://stedi.link/37ANBQ7?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=165&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve a root-level field with whitespace in its key

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/7w27dHL?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve a nested field with whitespace in its key

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/tgb8Axy?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 1.2 Operating on data

#### Concatenate two strings, separated by a space

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/Gb14LT3?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Multiply two values retrieved from an array together

>**Note:** It is not recommended to use JSONata for floating point arithmetic, such as financial calculations. Floating-point arithmetic can introduce rounding errors, which can accumulate and lead to incorrect results. Visit our [JSONata Playground](https://stedi.link/UKXfsxe) for an example.
>We recommend representing monetary values as integers (e.g. cents) and performing calculations on those integers.

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/k7Y9izo?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

(`[-1]` retrieves the last item in an array)

#### Remove a given character from a string

<iframe loading="lazy" allow="clipboard-write" width="100%" height="420" src="https://stedi.link/U5bfXkV?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert a value to a string

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/vixiEy9?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: (`$string` is a [JSONata function](/docs/cloud/mappings/jsonata/jsonata-functions/string#dollarstring))

#### Convert a value to a number

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/E0ij3ie?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: (`$number` is a [JSONata function](/docs/cloud/mappings/jsonata/jsonata-functions/numeric#dollarnumber))

### 1.3 String manipulation

#### Convert a value to a lowercase string

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/5V821EU?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert a value to an uppercase string

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/o95XgOL?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Truncate a string to only the first 4 characters

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/ekcs1Jh?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Check if a string contains a given substring

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/BJszTF9?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Get all characters in a string after a given substring

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/NgaZAUE?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Replace all `/`s with `-` in a string

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/AoJERxd?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Replace all `/`s with `-` in in a string (using `~>` operator)

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/cGbD1K0?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 1.4 Conditionals

#### Use a conditional to return a value based on a condition

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/Q5TexiO?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Use a conditional with a function to return a value based on a condition

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/yssLVtq?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 1.5 Filtering data

#### Filter data if a given value is greater than N

<iframe class="lazyload" allow="clipboard-write" width="100%" height="600" data-src="https://stedi.link/nLo50n8?sourcePanelHeight=250&expressionPanelHeight=100&outputPanelHeight=250&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Filter data if a given value is equal to X

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/KXTzvA0?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Filter data based on a complex condition

<iframe class="lazyload" allow="clipboard-write" width="100%" height="475" data-src="https://stedi.link/hvpwt58?sourcePanelHeight=250&expressionPanelHeight=100&outputPanelHeight=125&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: The `[]` is required at the end of an expression to convert the result to an array.

### 1.6 Counting data

#### Count fields based on a greater-than filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/oRqSOXq?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Count fields based on a substring value filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/may5Pot?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Count fields based on a substring value with `~>` operator

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/dkQjdxf?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Add up all values in an array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/diFNyf5?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Add up all values in an array with `~>` operator

>**Note:** It is not recommended to use JSONata for floating point arithmetic, such as financial calculations. Floating-point arithmetic can introduce rounding errors, which can accumulate and lead to incorrect results. Visit our [JSONata Playground](https://stedi.link/UKXfsxe) for an example.
>We recommend representing monetary values as integers (e.g. cents) and performing calculations on those integers.

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/smF14UG?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Use `$map`, `$sum` and `~>` operator to add up all values in an array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/j3i47Ay?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: [$map](/docs/cloud/mappings/jsonata/jsonata-functions/higher-order#dollarmap) is used to convert all items in `orders.volume` array to a [$number](/docs/cloud/mappings/jsonata/jsonata-functions/numeric#dollarnumber)

### 1.7 Variables

In JSONata, any name that starts with a `$` is a variable (e.g. `$streetName := address.street`). A variable can be one of any type in JSONata's [type system](https://docs.jsonata.org/processing#the-jsonata-type-system).

#### Built-in variables

- `$` – the variable with no name refers to the context value at any point in the input JSON hierarchy.
- `$$` – the root of the input JSON. You can use it to break out of the current context and navigate down a different path.

#### Convert a list of fields to the desired format using the built-in `$` variable

<iframe class="lazyload" allow="clipboard-write" width="100%" height="690" data-src="https://stedi.link/mcA6wdx?sourcePanelHeight=270&expressionPanelHeight=150&outputPanelHeight=270&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Populate an object field using the built-in `$$` variable for every item while looping over an array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="730" data-src="https://stedi.link/161A8sK?sourcePanelHeight=290&expressionPanelHeight=150&outputPanelHeight=290&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert value to a string and store it in a variable

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/6QMMrmG?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: `$variableName := value` is how assigns `value` to a JSONata `$variableName` variable

#### Count all items in an array and return a different result based on its size

<iframe class="lazyload" allow="clipboard-write" width="100%" height="500" data-src="https://stedi.link/2GfVeEn?sourcePanelHeight=250&expressionPanelHeight=150&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: a multi-line expression needs to be wrapped in `()`

#### Multiply values across all items in an array and return a boolean flag based on the result

<iframe class="lazyload" allow="clipboard-write" width="100%" height="540" data-src="https://stedi.link/33Sh3OZ?sourcePanelHeight=250&expressionPanelHeight=190&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Positional variables

[Positional variables binding](https://docs.jsonata.org/path-operators#-positional-variable-binding) can be used to determine at which position in the sequence the current context item is. It can be used following any map, filter or order-by stage in the path.

The variable is available for use within subsequent stages of the path (e.g. within filter predicates or a map operation) and goes out of scope at the end of the path expression.

#### Populate order title based on its index within the orders array

<iframe loading="lazy" allow="clipboard-write" width="100%" height="690" src="https://stedi.link/BFc9tOR?sourcePanelHeight=270&expressionPanelHeight=150&outputPanelHeight=270&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 1.8 Dates

#### Get the current date & time in ISO 8601 format

<iframe class="lazyload" allow="clipboard-write" width="100%" height="300" data-src="https://stedi.link/a8HfQ2N?sourcePanelHeight=100&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Get the current date & time in 6-character EDI date format

<iframe class="lazyload" allow="clipboard-write" width="100%" height="300" data-src="https://stedi.link/49kME0C?sourcePanelHeight=100&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Get the current date & time in 8-character EDI date format

<iframe class="lazyload" allow="clipboard-write" width="100%" height="300" data-src="https://stedi.link/uk1CDbJ?sourcePanelHeight=100&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert a date from `yyyy/MM/dd` format to `DD/MM/YYYY`

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/Nji8upT?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Get the month given a date in `yyyy/MM/dd` format

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/98rfWHE?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert a date taken from an array from `yyyy/MM/dd` format to `DD-MM-YYYY`

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/WcRjUMs?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert epoch date to EDI date format

<iframe class="lazyload" allow="clipboard-write" width="100%" height="300" data-src="https://stedi.link/X1yXx42?sourcePanelHeight=100&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Convert given UTC date to `America/New_York` timezone

<iframe class="lazyload" allow="clipboard-write" width="100%" height="300" data-src="https://stedi.link/RVuy0BE?sourcePanelHeight=100&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## 2. JSON array-of-objects source document

The source for each example is derived from the following JSON:

```json
[
  {
    "orderDate": "2021/10/12",
    "productID": "OIU98",
    "quantity": 100,
    "pricePerUnit": 1,
    "address": {
      "street": "1234 Main St.",
      "city": "Los Angeles",
      "state": "CA",
      "country name": "USA",
      "is-europe": false
    }
  },
  {
    "orderDate": "2021/01/07",
    "productID": "PWE47",
    "quantity": 45,
    "pricePerUnit": 500,
    "address": {
      "street": "La Rambla",
      "city": "Barcelona",
      "state": "Barcelona",
      "country name": "Spain",
      "is-europe": true
    }
  }
]
```

### 2.1 Retrieving data

#### Retrieve a field from the first item of a root-level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/WhRQyfp?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve an array of fields from a root-level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="500" data-src="https://stedi.link/mqjHTPr?sourcePanelHeight=250&expressionPanelHeight=100&outputPanelHeight=150&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve a nested value from a root-level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/uTdhFZf?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Retrieve a value with a space in its key from a root-level array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/cSLBQrF?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 2.2 Operating on data

The data operations expressions operate on the same basis as for the [JSON object source documents](#12-operating-on-data).

### 2.3 String manipulation

The string manipulation expressions operate on the same basis as for the [JSON object source documents](#13-string-manipulation).

### 2.4 Conditionals

The conditionals expressions operate on the same basis as for the [JSON object source documents](#14-conditionals).

### 2.5 Filtering data

The data filtering expressions operate on the same basis as for the [JSON object source documents](#15-filtering-data).

Instead of selecting an array field, you can select the root array with the `$$` selector.

#### Get an array of all items based on a filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="600" data-src="https://stedi.link/UdWXQjA?sourcePanelHeight=250&expressionPanelHeight=100&outputPanelHeight=250&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 2.6 Counting data

The data counting expressions operate on the same basis as for the [JSON object source documents](#16-counting-data).

Instead of selecting an array field, you can select the root array with the `$$` selector.

#### Count all items in an array based on a greater-than filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/VOw48DF?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Count all items in an array based on a substring filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/jCrfGPM?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

> Note: `$substring(orderDate, 0, 4)` returns first four characters of a string)

#### Count all items in an array based on a substring filter expressions using `~>` operator

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/EXmDTW5?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Sum up all items in an array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/w8or51e?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Sum up all items in an array based on a filter expression

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/qFUzwRG?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

#### Use `$map`, `$sum` and `~>` operator to add up all values in an array

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/Pv2FC65?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

### 2.7 Variables

Variables operate on the same basis as for the [JSON object source documents](#17-variables).

### 2.8 Dates

The dates expressions operate on the same basis as for the [JSON object source documents](#18-dates).
