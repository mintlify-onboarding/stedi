---
title: Numeric Operators
slug: cloud/mappings/jsonata/jsonata-operators/numeric
product: ""
description: ""
searchable: true
childPageOrder: []
---

## `+` (Addition)

The addition operator adds the operands to produce the numerical sum. It is an error if either operand is not a number.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/WxDGc66?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `-` (Substraction/Negation)

The subtraction operator subtracts the RHS value from the LHS value to produce the numerical difference It is an error if either operand is not a number.

It can also be used in its unary form to negate a number

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/6m3jxGL?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `*` (Multiplication)

The multiplication operator multiplies the operands to produce the numerical product. It is an error if either operand is not a number.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/icwp9TZ?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `/` (Division)

The division operator divides the RHS into the LHS to produce the numerical quotient. It is an error if either operand is not a number.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/kcoT4OG?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `%` (Modulo)

The modulo operator divides the RHS into the LHS using whole number division to produce a whole number quotient and a remainder. This operator returns the remainder. It is an error if either operand is not a number.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/LY8srBW?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `..` (Range)

The sequence generation operator is used to create an array of monotonically increasing integer start with the number on the LHS and ending with the number on the RHS. It is an error if either operand does not evaluate to an integer. The sequence generator can only be used within an array constructor \[].

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/2j0QLeb?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />
