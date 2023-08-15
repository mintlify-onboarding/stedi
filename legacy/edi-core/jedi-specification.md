---
title: JEDI Specification
slug: legacy/edi-core/jedi-specification
product: ""
description: ""
searchable: false
childPageOrder: []
---

# JEDI Specification

## Overview

JSON EDI (JEDI) is our proposed format for allowing customers to work with tools they are familiar with while still supporting conversion to and from EDI without any loss of fidelity. This document describes how a JEDI is structured and how it maps to an EDI file. This document assumes you have a general understanding of the EDI document format.

## Definitions

### Envelopes

An EDI transaction is wrapped in a series of 3 envelopes (a pre and post segment that wraps around inner information) that supply metadata about the transaction. Each envelope can be repeated so in the JSON form you will see arrays.

- Interchange control: (ISA/IEA) the outer most envelope. The data is at the top level of the JEDI document as the keys:
  - ISA: fields from the ISA segment
  - IEA: fields from the IEA segment
  - value: array of functional groups defined in this envelope
- Functional group: (GS/GE) in the 2nd layer and is represented as the envelopeGroup key at the top level. envelopeGroup is an array. Each object in the array has the following keys:
  - GS: fields from the GS segment
  - GE: fields from the GE segment
  - value: array of transactions defined in this envelope
- Transaction Set: (ST/SE) is the final layer that represents the an EDI transaction with the envelopeTransactionSet key
  - ST: fields from the ST segment
  - SE: fields from the SE segment
  - value: contents of the transaction

### Delimiters

EDI files are split by 3 different delimiters defined in the first (ISA) segment of the document. They are included in the document to ensure round trip rendering. There doesn't appear to be a standard for delimiters. The top level of a JEDI document contains a key called delimiters with the character used for each delimiter. Each delimiter must be a single character and unique within the delimiters. Often, EDI files have an additional \n at the end of each segment that is there for legibility but acts as whitespace.

- segment
- element
- subElement

#### Example

```EDI
ISA*00*          *00*          *ZZ*SENDER         *ZZ*RECEIVER       *201120*1204*U*00401*000000350*0*P*^~
```

Will extract the delimiters into

```JSON
{
  "segment": "~",
  "element: "*",
  "sub_element": "^",
  "repetition": "@"
}
```

### Elements

Individual data elements in a segment are called elements. The EDI documentation describes how the text of the element should be converted to numbers, text, or binary data. In JEDI, everything not defined as a number element will be rendered to the JSON as a string. Numbers will be saved as JSON numbers.

### Composites

Some elements are further broken into smaller pieces called sub elements. In this case, instead of scalar value in the JSON document, you will see another JSON object with keys from the subelements inside. All composite element ids start with a C followed by a 3 digit number.

### Segments

In EDI, a segment is prefixed by the segment id and then followed by 1 or more data elements separated by element delimiters and ended with a segment delimiter. In JEDI, the segment becomes a JSON object. The key of the segment in the JSON document will be its sequence id + the segment id. For example, line 5 in the EDI example below is an MIT segment. In JEDI, the key will be 100-MIT like it is described on edi.stedi.com.

#### Example (Segment)

The following SPT (Spirit and Liqueur Information) segment:

```EDI
SPT*AB*SG***01^^107***~
```

turns into:

```JSON
{
  "0100_SPT": {
    "type": "segment",
    "value": {
      "01_1823": { "type": "data_element", "value": "AB"},
      "02_1824": { "type": "data_element", "value": "GS" },
      "05_C075": {
        "type": "composite_data_element",
        "value": {
          "01_1827": { "type": "data_element", "value": "01" },
          "03_1827": { "type": "data_element", "value": "107" }
        }
      }
    }
  }
}
```

Notice that there is no entry in the map 03-1825 or 04-1826. Blank elements are not included in the map and will be rendered as an empty string when converting to EDI. The same thing happens inside of the composite element where 02-1827 is omitted.

### Repeated Segments

Many segments can be repeated. In this case, the child of the segment key, will be an array of segment objects instead of an object.

#### Example (Repeated Segment)

The following MSG segment:

```EDI
MSG*Your pizza is ready*NP
MSG*extra hot*NP
```

turns into

```JSON
{
  "0800_MSG": {
    "type": "segment_repeat",
    "value": [
      {
        "01_933": {"type": "data_element", "value": "Your pizza is ready" },
        "02_934": {"type": "data_element", "value": "NP" }
      },
      {
        "01_933": {"type": "data_element", "value": "extra hot" },
        "02_934": {"type": "data_element", "value": "NP" }
      }
    ]
}
```

### Loops

Some groups of segments are defined as loops in EDI. These will be shown as an array of segment maps.

#### Example (Loop)

The following MIT loop:

```EDI
MIT*999994627**80*55
MSG*Your pizza is ready*NP
```

turns into

```JSON
{
  "loop_MIT": {
    "type": "loop",
    "value": [
      {
        "0100_MIT": {
          "type": "segment",
          "value": {
            "01_127": {"type": "data_element", "value": "999994627" },
            "03_931": {"type": "data_element", "value": 80 },
            "04_932": {"type": "data_element", "value": 55 }
        }
      },
        "0800_MSG": {
          "type": "segment_repeat",
          "value": [
          {
            "01_933": {"type": "data_element", "value": "Your pizza is ready" },
            "02_934": {"type": "data_element", "value": "NP" }
          }
        ]
      }
    }
  ]
}
```

### Areas

The X12 definition for EDI put segments into 3 areas: heading, detail, and summary. The JEDI format uses the X12 data to put segments into the correct area.
