---
title: Translate X12 EDI to JSON
slug: legacy/edi-translate/translate-x12-edi-to-json
product: edi-translate
description: "With EDI Translate, you can translate X12 EDI into a JSON document. You can use a Stedi guide to describe the structure of the document."
searchable: false
childPageOrder: []
---

EDI Translate takes X12 EDI and turns it into a JSON document.
You may use a Stedi guide to describes the structure of the document.
Guides ensure that EDI exchanged between trading partners meets their needs.
The shape of the JSON document resembles the original X12 EDI, but you can map it into any shape you need.

You may also translate X12 EDI without a guide for more flexibility.
Review calling the API [without a guide](#without-a-guide) for more details.

- See also: [Create a Stedi guide](/docs/core/guides)
- See also: [Map a JSON document](/docs/mappings)

<!--- TODO
  Link to more specific topics once those pages have been written.
-->

## Envelopes

The interchange envelope ([ISA](https://www.stedi.com/edi/x12/segment/ISA)) and functional group envelope ([GS](https://www.stedi.com/edi/x12/segment/GS)) are optional, but if you provide either, you should provide both. The input EDI can optionally contain the transaction set envelope ([ST](https://www.stedi.com/edi/x12/segment/ST)).

At the moment, it isn’t possible to translate EDI documents with multiple functional groups. Multiple transaction sets within the same functional group works fine, though.

## Delimiters

The delimiters used during translation depend on the type of EDI document you submit.

- EDI document with ISA envelope.
  The delimiters specified in the ISA segment are used for parsing purposes.
  Delimiters specified in the guide or as request input options are ignore in this case.
- EDI document without ISA and GS envelopes.
  If EDI Translate is called with a guide, the delimiters specified in the guide will be used.
  If EDI Translate is called without a guide, the delimiters specified in `guidelessInputOptions` will be used.

The response of the [`to-json` endpoint](https://www.stedi.com/docs/api/edi-translate#TranslateJsonToX12) includes the delimiters used during processing of the EDI document.

## Calling the API

You translate a document by calling the API. First you need an [API key](/docs/accounts-and-billing/authentication) and then you can call the EDI Translate endpoint: `https://edi-translate.us.stedi.com/2022-01-01/x12/to-json`.

### With a Guide

```javascript
const axios = require("axios");

const apiKey = process.env.STEDI_API_KEY;

async function x12ToJson(guideId, document) {
  const response = await axios({
    method: "post",
    url: "https://edi-translate.us.stedi.com/2022-01-01/x12/to-json",
    data: {
      guideId: guideId,
      input: document,
    },
    headers: { Authorization: `Key ${apiKey}` },
  });

  return response.data;
}
```

### Without a Guide

Using EDI Translate without a Stedi guide lets you receive X12 EDI that might not conform to strict, partner-specific EDI requirements. Translation flexibility is useful for the following use cases:

- Developing and testing a new trading partner integration
- Troubleshooting EDI that doesn't meet your expectations
- Developing a guide to make sure EDI you receive meets your requirements

When you translate EDI without a Stedi guide, EDI Translate validates the incoming EDI with the generic X12 release specification and uses a default structure definition for the resulting JSON document. For example, the output will use default names for all properties.

Therefore, we do not recommend translating without a Stedi guide in production scenarios that require accurately processing partner-specific data. For example, updating an ERP system in response to incoming EDI may require specific fields for data consistency. In this case, you should use a guide to ensure these fields are present before the data is passed to the ERP system.

To translate without a guide, simply omit the `guideId` argument from the API call.

```javascript
async function x12ToJson(guideId, document) {
  const response = await axios({
    method: "post",
    url: "https://edi-translate.us.stedi.com/2022-01-01/x12/to-json",
    data: {
      input: document,
    },
    headers: { Authorization: `Key ${apiKey}` },
  });

  return response.data;
}
```

If your X12 EDI document does not have an ISA or GS envelope you will need to
specify an X12 release to use.

```javascript
async function x12ToJson(guideId, document) {
  const response = await axios({
    method: "post",
    url: "https://edi-translate.us.stedi.com/2022-01-01/x12/to-json",
    data: {
      input: document,
      inputOptions: {
        guidelessInputOptions: {
          x12Release: "004010",
        },
      },
    },
    headers: { Authorization: `Key ${apiKey}` },
  });

  return response.data;
}
```
