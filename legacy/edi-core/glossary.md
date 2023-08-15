---
title: Glossary
slug: legacy/edi-core/glossary
product: edi-core
description: ""
searchable: false
childPageOrder: []
---

# Glossary

## Composite element

A single [element](#element) that contains multiple values of different types, similar to a struct or record. The [specification](#specification) will specify which fields makes up a composite element. The values in a composite element are kept apart by a [delimiter](#delimiter). The delimiter to use is specified in the [interchange](#interchange) header. In the following example, values are delimited using a `:`.

```
PRV*LA***ORT:HS:Y~
```

## Delimiter

A delimiter is used in the [X12 EDI Format](#x12-edi-format) to mark the end of a [segment](#segment) or [element](#element). The end of a [segment](#segment) is [delimited](#delimiter) by a `~` and the end of an [element](#element) by a `*`. Here's an example of two segments, both with three elements.

```edi
SE*2*0000~
GE*1*987654321~
```

There are also delimiters for [repeated elements](#repeated-element) and [composite elements](#composite-element), but these aren't standardized. Instead, these delimiters are specified in the [interchange](#interchange).

## Element

An element is a data field within a [segment](#segment). In the [X12 EDI Format](#x12-edi-format), elements in a [segment](#segment) are delimited by a `*`. For example, here's a [segment](#segment) with several elements.

```edi
GS*SO*00*00*20210902*1200*987654321*X*008010~
```

The values an element may contain, are described in a [specification](#specification).

## EDI (Electronic Data Interchange)

EDI allows businesses to exchange electronic documents, like invoices, insurance applications, train sheets, credit reports. There are several standards related to EDI. Most relevant to working with Stedi are the [specifications](#specification) of [transaction sets](#transaction-set) and the [X12 EDI Format](#x12-edi-format).

## Functional group

Within an [EDI](#edi) file, a functional group is a set of [transaction sets](#transaction-set). Typically, all the [transaction sets](#transaction-set) within a functional group are intended for the same department within an organization.

The beginning of a functional group is marked by the [GS](https://edi.stedi.com/x12-008010/segment/GS) [segment](#segment) and the end is marked by the [GE](https://edi.stedi.com/x12-008010/segment/GE) \[segment]\[#segment]. A functional group must be part of an [interchange](#interchange).

## Interchange

Within an [EDI](#edi) file, an interchange is a set of [functional groups](#functional-group) which in turn contain [transaction sets](#transaction-set). Typically, the contents of an interchange is intended for a specific organization and the contents of a [functional group](#functional-group) is intended for a specific department within that organization.

The beginning of an interchange is marked by the [ISA](https://edi.stedi.com/x12-008010/segment/ISA) [segment](#segment) and the end is marked by the [IEA](https://edi.stedi.com/x12-008010/segment/IEA) [segment](#segment). Every EDI file must have at least one interchange and consequently, every EDI file starts with an [ISA](https://edi.stedi.com/x12-008010/segment/ISA) [segment](#segment) and ends with an [IEA](https://edi.stedi.com/x12-008010/segment/IEA) [segment](#segment).

## JEDI (JSON EDI)

A file format for encoding [EDI](#edi) data in a JSON representation, developed by Stedi. You can find details in the [JEDI Format Guide](https://www.stedi.com/docs/edi-core/jedi-format).

## Loop

A loop is a collection of [segments](#segment) that can appear multiple times in a [transaction set](#transaction-set). The [segments](#segment) in a loop typically have a semantic relationalship, for example, the segments Party Identification, Geographic Location, and Communication Contact Information together can tell you about a single [trading partner](#trading-partner) and a loop of those segments allows you to refer to multiple [trading partners](#trading-partner) in a [transaction set](#transaction-set).

In [X12 EDI Format](#x12-edi-format), there's no special [delimiter](#delimiter) for loops, nor is there a set of [segments](#segment) that mark the begin and end of the loop, like there is for [interchanges](#interchange), [functional groups](#functional-group), and [transaction sets](#transaction-set). Instead, the segments in the loop are just repeated.

```edi
N1*21*Stedi*ZZ*ID001~
N4*New York**US~
COM*16*stedi.com~
N1*21*Basic Bits*ZZ*ID002~
N4*Haarlem**NL~
COM*16*basicbits.tech~
```

## Repeated element

A single [element](#element) that contains multiple values of the same type, similar to a homogeneous array. Not every [element](#element) can be repeated; the [specification](#specification) must indicate that it's okay to do so.

In [X12 EDI Format](#x12-edi-format), the values in a repeated element are kept apart by a [delimiter](#delimiter). The delimiter to use is specified in the [interchange](#interchange) header. In the following example, values are delimited using a `/`.

```edi
AAA*Y***NF/NR/TO~
```

## Repeated segment

Within an [EDI](#edi) file, some segments can occur multiple times in a row. The [specification](#specification) will tell when a segment can be repeated.

In [X12 EDI Format](#x12-edi-format)], a repeated segment appears as, well, a segment that's repeated.

```edi
NTE**When a segment has need to repeat,~
NTE**How does one accomplish that feat?~
NTE**It's really no bother.~
NTE**One after the other.~
NTE**A solution that's simple and neat.~
```

## Segment

Within an [EDI](#edi) file, a segment is a set of [elements](#element), where the segment is like a record and the [elements](#element) are its fields. Each segment starts with a segment identifier that tells you which [elements](#element) will follow. The different types of segments and their elements are listed in the [specification](#specification).

In the [X12 EDI Format](#x12-edi-format), segments are [delimited](#delimiter) by a `~`. It's also common to add a new line character after the [delimiter](#delimiter), but that's for human readability only and has no semantic value.

## Specification

A specification describes the data schema of a [transaction set](#transaction-set). It lists the [loops](#loop) and [segments](#segment), the order in which they appear, and the [elements](#element) within the [segments](#segment). The specification gives you all the information you need to write or interpret an [EDI](#edi) document that conforms to that specification.

[X12](#x12) has published [many specifications](https://edi.stedi.com/). A [trading partner](#trading-partner) can use these specifications directly, or they can base their own specification on one of them.

## Trading partner

Any business that sends or receives business transactions.

## Transaction set

A transaction set is a type of business document, for example a purchase order, or a booking cancellation. Each transaction set is based on a [specification](#specification) that describes what data the transaction set can and must contain. There is a [list of standardized transaction sets](https://edi.stedi.com/).

The standard transaction sets are designed to be widely applicable. A [trading partner](#trading-partner) can take a transaction set, base its own [specification](#specification) on it, and only accept the data it knows how to handle. Many of these [business-specific transaction sets are available on Stedi](https://edi.stedi.com/specifications).

## X12

An organization that has published many standards related to [EDI](#edi).

## X12 EDI Format

A file format for encoding [EDI](#edi) data, standardized by [X12](#x12). It's text-based and makes use of [delimiters](#delimiter) to mark the [segments](#segment) and [elements](#element). It looks something likes this.

```edi
ISA*00*          *00*          *ZZ*               *ZZ*               *210902*1200*|*00801*123456789*1*T*:~
GS*SO*00*00*20210902*1200*987654321*X*008010~
ST*326*0000~
SE*2*0000~
GE*1*987654321~
IEA*1*123456789~
```
