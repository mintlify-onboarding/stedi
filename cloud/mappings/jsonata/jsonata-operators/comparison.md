---
title: Comparison Operators
slug: cloud/mappings/jsonata/jsonata-operators/comparison
product: ""
description: ""
searchable: true
childPageOrder: []
---

## `=` (Equals)

The equality operator returns Boolean `true` if both operands are the same (type and value). Arrays and objects are checked for deep equality. Arrays must have the same values in the same order. Objects must have the same key/value pairs (order is not relevant). Otherwise it returns `false`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/FWzqg9d?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `!=` (Not equals)

The inequality operator returns Boolean `false` if both operands are the same (type and value, deep equality). Otherwise it returns `true`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/GvzdSMx?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `>` (Greater than)

The 'greater than' operator returns Boolean `true` if the LHS is numerically greater than the RHS. Otherwise it returns `false`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/npUNfaa?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `<` (Less than)

The 'less than' operator returns Boolean `true` if the LHS is numerically less than the RHS. Otherwise it returns `false`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/DYHOhQ7?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `>=` (Greater than or equals)

The 'greater than or equals' operator returns Boolean `true` if the LHS is numerically greater than or equal to the RHS. Otherwise it returns `false`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/tgNWiMy?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `<=` (Less than or equals)

The 'less than or equals' operator returns Boolean `true` if the LHS is numerically less than or equal to the RHS. Otherwise it returns `false`.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/O4VieKY?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />

## `in` (Inclusion)

The array (sequence) inclusion operator returns Boolean `true` if the value of the LHS is included in the array of values on the RHS. Otherwise it returns `false`. If the RHS is a single value, then it is treated as a singleton array.

**Example**

<iframe loading="lazy" allow="clipboard-write" width="100%" height="200" src="https://stedi.link/36ezOvX?sourcePanelHeight=0&expressionPanelHeight=100&outputPanelHeight=100&lazy=true" title="Stedi JSONata Playground" frameBorder="0" />
