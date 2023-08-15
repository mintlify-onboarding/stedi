---
title: Common Mapping Expressions
slug: cloud/mappings/jsonata/common-mapping-expressions
description: Create mappings to translate JSON data from one shape to another.
sidebarTitle: "Common Mapping Expressions"
searchable: true
childPageOrder: []
---

For every field in your target, you need to write an expression that specifies how to turn input fields into an output field. The following example shows how you could output a total price from a quantity and a unit price.

```
{ "total": item.quantity * item.unit_price }
```

Refer to [Mapping Definition](/docs/cloud/mappings/manage-mappings/mapping-definition) for more details about mapping components.

This page demonstrates mapping expressions that address common use cases. Refer to the following resources for more examples.

- [Mapping expression cheatsheet][cheatsheet]: A collection of more common patterns for mapping expressions
- [JSONata documentation][jsonata-docs]: A description of the JSONata language

## Basic mapping expressions

You start with a target field and write a mapping expression to select the corresponding source field. The simplest mapping expression points to a single source field.

**Source**

```json
{
  "telephone": "+(1)(303) 555-0100"
}
```

**Target**

```json
{
  "phone_number": "+(1)(303) 555-0100"
}
```

**Expression**

| Target field   | Mapping expression |
| -------------- | ------------------ |
| `phone_number` | `telephone`        |

The output will contain the key `phone_number`. In this case, the mapping expression is simply the name of a field in the source.

### Nested source fields

Use a path to select nested source fields

**Source**

```json
{
  "business": {
    "contact": {
      "telephone": "+(1)(303) 555-0100"
    }
  }
}
```

**Target**

```json
{
  "phone_number": "+(1)(303) 555-0100"
}
```

**Expression**

| Target field   | Mapping expression           |
| -------------- | ---------------------------- |
| `phone_number` | `business.contact.telephone` |

A path contains the key names at every level of a field, separated by dots.

It can be tedious to write out the path for every mapping expression, especially if you have a source with a deeply nested structure. In the Mappings UI, you can click on the field in the source to copy the path to your clipboard.

## Lists

To map a list in the source to a list in the target, you need to take two steps.

1. Write a mapping expression to specify which list you need from the source.
2. Write a mapping expression for each field inside the list of your target.

**Source**

```json
{
  "transaction": {
    "order": {
      "products": [
        {
          "id": "QL-5490S",
          "amount": 17,
          "price": {
            "unit": 840,
            "total": 14280
          }
        },
        {
          "id": "LV-69200",
          "amount": 91,
          "price": {
            "unit": 15,
            "total": 1365
          }
        },
        {
          "id": "RD-0392P",
          "amount": 1,
          "price": {
            "unit": 930,
            "total": 930
          }
        }
      ]
    }
  }
}
```

**Target**

```json
{
  "orders": [
    {
      "product_number": "FF08CD",
      "quantity": 1,
      "unit_price": 20
    }
  ]
}
```

**Expression**

In the target, the list is called `orders`. In the mappings UI, the field is marked with the word **array**, which is another word for list. In the source, the list that contains the relevant data has the path `transaction.order.products`, so that's the mapping expression you need for `orders`.

Specify a mapping expression for each field in `orders`. For example, the field `product_number` is called `id` in the source.

| Target field     | Mapping expression           |
| ---------------- | ---------------------------- |
| `orders`         | `transaction.order.products` |
| `product_number` | `id`                         |
| `quantity`       | `amount`                     |
| `unit_price`     | `price.unit`                 |

The mapping expressions for the fields in the list don't include `transaction.order.products`, because Mappings knows that those fields are relative to the context of the list. For that reason, the mapping expression for a list is referred to as a _list context_.

### List indexes

If the list entries of your target document are expected to include a list index number, you can access it by binding a _positional variable_ to the List Context.

> **Info**: Read about _positional variable binding_ in the [JSONata docs](https://docs.jsonata.org/path-operators#-positional-variable-binding).

For example, imagine you have the same source document as in the previous example, but the target document contains a new `index` property:

**Source**

```json
{
  "transaction": {
    "order": {
      "products": [
        {
          "id": "QL-5490S",
          "amount": 17,
          "price": {
            "unit": 840,
            "total": 14280
          }
        },
        {
          "id": "LV-69200",
          "amount": 91,
          "price": {
            "unit": 15,
            "total": 1365
          }
        },
        {
          "id": "RD-0392P",
          "amount": 1,
          "price": {
            "unit": 930,
            "total": 930
          }
        }
      ]
    }
  }
}
```

**Target**

```json
{
  "orders": [
    {
      "product_number": "FF08CD",
      "index": 1,
      "quantity": 1,
      "unit_price": 20
    }
  ]
}
```

**Expression**

To get access to the list index number, you need to define a _positional variable_ for your List Context first. Then, you can use it in your mapping expression for the `index` field. Positional variables in JSONata are zero-based.

| Target field     | Mapping expression                    |
| ---------------- | ------------------------------------- |
| `orders`         | `transaction.order.products#$myIndex` |
| `product_number` | `id`                                  |
| `index`          | `$myIndex + 1`                        |
| `quantity`       | `amount`                              |
| `unit_price`     | `price.unit`                          |

Once the expression gets evaluated, you can verify that the `index` property is successfully populated for every item in the Output JSON document. This approach could create output like the following example.

```json
{
  "orders": [
    {
      "product_number": "QL-5490S",
      "index": 1,
      "quantity": 17,
      "unit_price": 840
    },
    {
      "product_number": "LV-69200",
      "index": 2,
      "quantity": 91,
      "unit_price": 15
    },
    {
      "product_number": "RD-0392P",
      "index": 3,
      "quantity": 1,
      "unit_price": 930
    }
  ]
}
```

### Lists with one value

If a list contains only one value, it will show up in the output as a single value instead of as a list. Consider the following example.

**Source**

```json
{
  "products": [
    {
      "id": "QL-5490S"
    }
  ]
}
```

**Target**

```json
{
  "product_numbers": ["FF08CD", "RX66PL"]
}
```

**Expression**

| Target field      | Mapping expression |
| ----------------- | ------------------ |
| `product_numbers` | `products.id`      |

You'd expect the output to be a list, just like the target example, but because there's only one product, the result is a single value.

```json
{
  "product_numbers": "QL-5490S"
}
```

If you want to make sure that the result is always an array, put `[]` at the end of the mapping expression. The following example shows the new output.

```json
{
  "product_numbers": ["QL-5490S"]
}
```

## Objects

In a generic case, to map an object in the source to an object in the target, you don't need to do anything on the object level, you only need to define expressions for each field inside of your target object.

**Source**

```json
{
  "product":
    {
      "id": "QL-5490S",
      "amount": 17,
      "price": {
        "unit": 840,
        "total": 14280
      }
    }
  }
}
```

**Target**

```json
{
  "order": {
    "product_number": "FF08CD",
    "quantity": 1,
    "unit_price": 20
  }
}
```

**Expression**

Specify a mapping expression for each field within the `order` object.

| Target field     | Mapping expression   |
| ---------------- | -------------------- |
| `order`          |                      |
| `product_number` | `product.id`         |
| `quantity`       | `product.amount`     |
| `unit_price`     | `product.price.unit` |

There is no expression specified on the `order` level, so all of its children have to specify a path relative to the root of the source document.

## Object context

Object context is an optional expression that can be provided for any field which contains a single JSON object.

By providing an object context you can improve your mappings in two ways:

1. Avoid repetition of the data transformation within child field expressions.
2. Omit whole objects from the output based on a condition.

### Avoiding repetition within child field expressions

Another way to solve the same mapping from the previous example, would be to provide an _object context_ for the `order`, and remove the common part of the path from expressions of its child fields.

| Target field     | Mapping expression |
| ---------------- | ------------------ |
| `order`          | `product`          |
| `product_number` | `id`               |
| `quantity`       | `amount`           |
| `unit_price`     | `price.unit`       |

Removal of the repeated path prefix was not so dramatic, but imagine you have to map a particular member of an array in the source document, to a single object in the output document.

**Source**

```json
{
  "products": [
    {
      "id": "QL-5490S",
      "amount": 17,
      "price": {
        "unit": 840,
        "total": 14280
      }
    },
    {
      "id": "LV-69200",
      "amount": 91,
      "price": {
        "unit": 15,
        "total": 1365
      }
    },
    {
      "id": "RD-0392P",
      "amount": 1,
      "price": {
        "unit": 930,
        "total": 930
      }
    }
  ]
}
```

**Target**

```json
{
  "order": {
    "product_number": "FF08CD",
    "quantity": 1,
    "unit_price": 20
  }
}
```

**Expression**

In this example, we are only interested in a product with ID starting with `LV-` prefix.

| Target field     | Mapping expression                            |
| ---------------- | --------------------------------------------- |
| `order`          | `products[$startsWith(id, "LV-")] ~> $single` |
| `product_number` | `id`                                          |
| `quantity`       | `amount`                                      |
| `unit_price`     | `price.unit`                                  |

The mapping expressions for the fields in the list don't include the filter expression, because Mappings knows that those fields are relative to the context of the object. For that reason, the optional mapping expression for an object is referred to as an _object context_.

### Conditionally omitting objects

Mappings allows to skip an object with all of its child fields from the output based on a condition. To achieve that, specify a custom object context that evaluates to an [$omitField constant](#dollaromitfield) when your desired conditions are met.

Let's consider a case, where the source document may contain an array of products of variable length, and if the count of products in the source is `0`, a certain object should not be populated in the output.

**Source**

```json
{
  "customer": "John Doe",
  "products": [
    {
      "id": "QL-5490S",
      "amount": 17,
      "price": {
        "unit": 840,
        "total": 14280
      }
    },
    {
      "id": "LV-69200",
      "amount": 91,
      "price": {
        "unit": 15,
        "total": 1365
      }
    },
    {
      "id": "RD-0392P",
      "amount": 1,
      "price": {
        "unit": 930,
        "total": 930
      }
    }
  ]
}
```

**Target**

```json
{
  "customer_name": "Jane Doe",
  "order": {
    "quantity": 1,
    "total_price": 20
  }
}
```

**Expression**

To omit the `order` object from the output, you should provide a ternary condition as an optional object context, and return `$omitField` when zero products were found in the source.

When the omitting condition is not met, you can pass down the parent context variable `$`, which in this case would evaluate to the whole source document, the same as not providing and object context at all.

| Target field    | Mapping expression                      |
| --------------- | --------------------------------------- |
| `customer_name` | `customer`                              |
| `order`         | `$count(products) = 0 ? $omitField : $` |
| `quantity`      | `$count(products)`                      |
| `total_price`   | `$sum(products.price.total)`            |

## Advanced mapping expressions

Mappings allows you to do more advanced things than selecting fields. Unless you're an experienced programmer, writing complex mapping expressions will take some getting used to. We'll provide some common patterns here. If you're looking for more, check out our [mapping expressions cheatsheet][cheatsheet].

> **Tip**: Advanced mapping expressions can get quite long. Click on the green icon next to the mapping expression you're editing to open the fullscreen view. This will give you more space to work with.

### Text to number

Sometimes, your source will have quotes around a number. When that happens, Mappings thinks it's dealing with text. You can convert the text to a number by using the `$number` function.

**Source**

```json
{
  "quantity": "8"
}
```

**Target**

```json
{
  "quantity": 8
}
```

**Expression**

| Target field | Mapping expression  |
| ------------ | ------------------- |
| `quantity`   | `$number(quantity)` |

### Number to text

If your target contains a number in quotes, then to Mappings, that's text instead of a number. You can convert the number to text by using the `$string` function. String is another word for text.

**Source**

```json
{
  "quantity": 8
}
```

**Target**

```json
{
  "quantity": "8"
}
```

**Expression**

| Target field | Mapping expression  |
| ------------ | ------------------- |
| `quantity`   | `$string(quantity)` |

### Calculations

You can do calculations on numbers using `*`, `/`, `+`, and `-`.

**Source**

```json
{
  "price": 500,
  "quantity": 8,
  "discount": 150
}
```

**Target**

```json
{
  "total": 3850
}
```

**Expression**

| Target field | Mapping expression            |
| ------------ | ----------------------------- |
| `total`      | `price * quantity * discount` |

If the numbers in the source are surrounded by quotes, you need to convert them first using the `$number` function.

**Source**

```json
{
  "subtotal": "500",
  "vat": "10"
}
```

**Target**

```json
{
  "total": 510
}
```

**Expression**

| Target field | Mapping expression                 |
| ------------ | ---------------------------------- |
| `total`      | `$number(subtotal) * $number(vat)` |

### Sum and average

You can calculate the sum and average of a list of numbers using `$sum` and `$average`.

**Source**

```json
{
  "prices": [14280, 1365, 930]
}
```

**Target**

```json
{
  "sum": 16575,
  "average": 5525
}
```

**Expression**

| Target field | Mapping expression |
| ------------ | ------------------ |
| `sum`        | `sum(prices)`      |
| `average`    | `average(prices)`  |

Often, the numbers you're interested in are part of a more complex structure.

**Source**

```json
{
  "orders": [
    {
      "product_id": "QL-5490S",
      "price": 14280
    },
    {
      "product_id": "LV-69200",
      "price": 1365
    },
    {
      "product_id": "RD-0392P",
      "price": 930
    }
  ]
}
```

**Target**

```json
{
  "sum": 16575,
  "average": 5525
}
```

**Expression**

| Target field | Mapping expression         |
| ------------ | -------------------------- |
| `sum`        | `$sum(orders[].price)`     |
| `average`    | `$average(orders[].price)` |

In this case, you can refer to the field you're interested in by its full path (`price`). Make sure to put `[]` after the name of the list (`orders`) to let Mappings know that you want all prices in the list.

### Putting text together

When you have to two text fields and you want to put them together, you can use `&`.

**Source**

```json
{
  "first_name": "Alice",
  "last_name": "Mahara"
}
```

**Target**

```json
{
  "name": "Alice Mahara"
}
```

**Expression**

| Target field | Mapping expression             |
| ------------ | ------------------------------ |
| `name`       | `first_name & " " & last_name` |

### Taking text apart

You can extract a small part out of a text by using `$substring`. You need to specify where the part is that you're interested in, so this only works for text that follows a predictable pattern.

**Source**

```json
{
  "phone_number": "(303) 555-0100"
}
```

**Target**

```json
{
  "area_code": "303"
}
```

**Expression**

| Target field | Mapping expression              |
| ------------ | ------------------------------- |
| `area_code`  | `substring(phone_number, 1, 3)` |

The two numbers let `$substring` know where the part begins, and how many letters you want. `$substring` starts counting characters at 0, so in the example above, we start at the second characters.

If the part you're interested in is at the back of the text, you can use a negative number to tell `$substring` to start counting from the last character.

**Source**

```json
{
  "phone_number": "(303) 555-0100"
}
```

**Target**

```json
{
  "local_number": "555-0100"
}
```

**Expression**

| Target field   | Mapping expression                |
| -------------- | --------------------------------- |
| `local_number` | `$substring(phone_number, -8. 8)` |

### Splitting text

Sometimes a text contains multiple pieces of data, separated by a character. You can turn the text into a list using `$split`.

**Source**

```json
{
  "location": "Chicago, Illinois, United States"
}
```

**Target**

```json
{
  "location": ["Chicago", "Illinois", "United States"]
}
```

**Expression**

| Target field | Mapping expression       |
| ------------ | ------------------------ |
| `location`   | `$split(location, ", ")` |

You can assign each item in the list to a field by using an _index_, which is a number between square brackets. Items in a list are numbered starting at 0.

**Source**

```json
{
  "location": "Chicago, Illinois, United States"
}
```

**Target**

```json
{
  "city": "Chicago",
  "state": "Illinois",
  "country": "United States"
}
```

**Expression**

| Target field | Mapping expression          |
| ------------ | --------------------------- |
| `city`       | `$split(location, ", ")[0]` |
| `state`      | `$split(location, ", ")[1]` |
| `country`    | `$split(location, ", ")[2]` |

### Lookup table

If you have a field that contains a code that you want to replace with a related value, you can build a lookup table.

**Source**

```json
{
  "country_code": "USA"
}
```

**Target**

```json
{
  "country": "United States"
}
```

Before you can write a mapping expression for this, you'll need to create the lookup table.

1. Clicking the edit icon next to the mapping expression.
1. Click **Lookup tables** and select **Add new**.
1. Enter values for your table. You can add values manually, or load them from CSV.

Now you can use the lookup table in your mapping expressions using the function `$lookupTable`. For example, you might create a lookup table for country codes and then write the following expression.

| Target field   | Mapping expression                                           |
| -------------- | ------------------------------------------------------------ |
| `country_code` | `$lookupTable($tables.countries, "short" country_code).long` |

A lookup table isn't limited to two values per entry; a row can have as many values as you need. For example, you could create a currency lookup table with two columns: `code` for the country code and `symbol` for the currency symbol.

**Source**

```json
{
  "currency": "USD"
}
```

**Target**

```json
{
  "currency": {
    "name": "U.S. Dollar",
    "symbol": "$"
  }
}
```

**Expression**

| Target field | Mapping expression                                        |
| ------------ | --------------------------------------------------------- |
| `name`       | `$lookupTable($tables.currency, "code", currency).name`   |
| `symbol`     | `$lookupTable($tables.currency, "code", currency).symbol` |

### Lookup table wildcards

You can use Lookup Tables and wildcards for matching multiple possible input options at once.

In your lookup table, replace the interchangeable part of the key you want to match against with the `*` symbol (or any other sequence of symbols, you will be able to select what to match against during the `$lookupTable` function call).

-> **Note:** You can replace multiple parts of your key with `*`.

Any input value that matches the loosely defined wildcard-based lookup table value is now matched when the `{ "wildcard": "*" }` is passed as an optional parameter to the `$lookupTable` function.

## Mapping types

The mapping type specifies how the Mappings API generates the output field when the mapping expression doesn't produce a value. A mapping expression may not produce a value when one or more of the input fields that the mapping expression depends on aren't present.

You can choose between the following mapping types:

- Only mapped keys
- Merge with target example
- Pass through

Visit [Mapping Definition Overview](/docs/cloud/mappings/manage-mappings/mapping-definition#mapping-types) for full details and examples of how the Mappings UI generates outputs in each case.

## Omitting output fields

There are times when a field is present in the target, but you don't want it to end up in the output. You have two options.

- Deselect the target field.
- Use the `$omitField` constant.

### Deselecting target fields

If you don't provide a mapping expression for a target field, or if a mapping expression doesn't produce a result, the field may still end up in the output. If you don't want that, you can deselect the target field.

In the following example, none of the fields in the `totals` object has a mapping expression associated with it, but it still ends up in the output.

**Target**

```json
{
  "products": [
    {
      "id": "FF08CD"
    }
  ],
  "totals": {
    "quantity": 3,
    "price": 8850
  }
}
```

| Target field | Mapping expression |
| ------------ | ------------------ |
| `products`   | `products`         |
| `id`         | `id`               |
| `quantity`   |                    |
| `price`      |                    |

**Output**

```json
{
  "products": [
    {
      "id": "QL-5490S"
    }
  ],
  "totals": {}
}
```

If you don't want `totals` to show up at all, select **Target keys** and deselect the field.

This doesn't apply when you set the mapping type to _Merge with target example_, because that option will always copy the values from the the target, unless you use `$omitField`.

### $omitField

Whether a target field should end up in the output is not always a simple yes-or-no question. Sometimes, it depends on the result of the mapping expression. In that case you can use `$omitField` to tell Mappings when to skip the field.

This is particularly useful if the mapping type is set to _Merge with target example_ and you don't want to use the default value. In the following example, the total price is included only if the amount of products is larger than 0.

**Target**

```json
{
  "totalPrice": 3000,
  "unitPrice": 150
}
```

**Mapping Expression**

| Target field | Mapping expression                         |
| ------------ | ------------------------------------------ |
| `totalPrice` | `amount > 0 ? amount * price : $omitField` |
| `unitPrice`  | `price`                                    |

**Input**

```json
{
  "price": 150,
  "amount": 0
}
```

**Output**

```json
{
  "unitPrice": 150
}
```

Without `$omitField`, the output would've included the `totalPrice` with its default value of `3000`, which is clearly wrong in this case.

> **Note**: You can use `$omitField` in any input field on the Mapping form, including [list context](#lists) and [object context](#object-context) inputs.

[mappings-product]: https://stedi.com/app/mappings
[edi-translate-docs]: /docs/legacy/edi-core#translation
[cheatsheet]: /docs/cloud/mappings/jsonata/jsonata-cheatsheet
[jsonata-docs]: https://docs.jsonata.org/overview
