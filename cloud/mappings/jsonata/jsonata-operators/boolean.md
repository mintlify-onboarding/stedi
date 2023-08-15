---
title: Boolean Operators
slug: cloud/mappings/jsonata/jsonata-operators/boolean
product: ""
description: ""
searchable: true
childPageOrder: []
---

## `and` (Boolean AND)

The 'and' operator returns Boolean `true` if both operands evaluate to `true`. If either or both operands is not a Boolean type, then they are first cast to a Boolean using the rules of the `$boolean` function.

**Example**

<iframe class="lazyload" allow="clipboard-write" width="100%" height="420" data-src="https://stedi.link/1vPjc9k?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `or` (Boolean OR)

The 'or' operator returns Boolean `true` if either operand evaluates to `true`. If either or both operands is not a Boolean type, then they are first cast to a Boolean using the rules of the `$boolean` function.

**Example**

<iframe class="lazyload" allow="clipboarnd-write" width="100%" height="420" data-src="https://stedi.link/iSHVzHN?sourcePanelHeight=220&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

**Please note that Boolean 'NOT' is a [function](https://www.stedi.com/docs/cloud/mappings/jsonata/jsonata-functions/boolean#dollarnot), not an operator.**
