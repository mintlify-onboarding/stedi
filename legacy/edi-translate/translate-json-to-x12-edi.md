---
title: Translate JSON to X12 EDI
slug: legacy/edi-translate/translate-json-to-x12-edi
product: edi-translate
description: "With EDI Translate, you can translate a JSON document into X12 EDI. You need a Stedi guide to describe the structure of the JSON."
searchable: false
childPageOrder: []
---

EDI Translate takes a JSON document that’s already structured like EDI and turns it into an X12 EDI document. You need a Stedi guide that describes the structure. If you have a JSON document that’s not structured like EDI, you have to map it first.

- See also: [Create a Stedi guide](/docs/core/guides)
- See also: [Map a JSON document](/docs/mappings)

<!--- TODO
  Link to more specific topics once those pages have been written.
-->

## JSON structure

The Stedi guide determines how the input JSON should be structured. If you need an example of the JSON structure, you can [open your Stedi guide](https://www.stedi.com/app/guides/) and go to the _EDI Inspector_ tab. It will show you an example of both the EDI and the JSON.

## Envelope

EDI documents have envelopes that contain metadata. That metadata is typically not included in the JSON document, so you need to provide it separately.

There are three types of envelopes.

- The interchange ([ISA](https://www.stedi.com/edi/x12/segment/ISA)) tells you who the message is for on an organization level. It identifies you as the sender and your trading partner as the receiver.
- The functional ([GS](https://www.stedi.com/edi/x12/segment/GS)) group tells you who the message is for on a department level. Invoices go to the Finance department, ship notices go to the Shipping department, that sort of thing.
- The transaction set ([ST](https://www.stedi.com/edi/x12/segment/ST)) represents a single document. An EDI message can contain multiple documents and the transaction set envelope helps keep them apart.

Each envelope has a start segment and an end segment. EDI Translate will generate these segments based on the data you provide.

```json
{
  "interchangeHeader": {
    "senderQualifier": "ZZ",
    "senderId": "ME",
    "receiverQualifier": "ZZ",
    "receiverId": "YOU",
    "date": "2022-08-15",
    "time": "13:20",
    "usageIndicatorCode": "P",
    "controlNumber": "12"
  },
  "groupHeader": {
    "functionalIdentifierCode": "PO",
    "applicationSenderCode": "ME",
    "applicationReceiverCode": "YOUR DEPT",
    "date": "2022-08-15",
    "time": "13:20:52",
    "controlNumber": "1"
  }
}
```

| Interchange field  | Description                                                                                                                                                                                                                                                |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| senderId           | Identifies the sender of the message.                                                                                                                                                                                                                      |
| senderQualifier    | Indicates the type of identifier used in `senderId`. For example, `01` means Duns, `20` means Health Industry Number, `ZZ` whatever you agree on. The implementation guide should tell you which values are valid. If not, contact your trading partner.   |
| receiverId         | Identifies the receiver of the message.                                                                                                                                                                                                                    |
| receiverQualifier  | Indicates the type of identifier used in `receiverId`. For example, `01` means Duns, `20` means Health Industry Number, `ZZ` whatever you agree on. The implementation guide should tell you which values are valid. If not, contact your trading partner. |
| date               | The date this message was sent, e.g. `2022-08-15`.                                                                                                                                                                                                         |
| time               | The time this message was sent, e.g. `13:20`.                                                                                                                                                                                                              |
| usageIndicatorCode | `P` if this is a production message, `T` if it’s a test message, `I` if the message is informational.                                                                                                                                                      |
| controlNumber      | A unique identifier for this interchange.                                                                                                                                                                                                                  |

| Functional group field   | Description                                                                                                                                                                                                            |
| ------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| functionalIdentifierCode | Indicates the type the message. Must be one of the [standardized functional identifier code](https://www.stedi.com/edi/x12/element/479).                                                                               |
| applicationSenderCode    | Indicates where the message came from. There are no standardized codes; you need to agree on valid values with your trading partner. If you can’t find this in the implementation guide, contact your trading partner. |
| applicationReceiverCode  | Indicates where the message should go. There are no standardized codes; you need to agree on valid values with your trading partner. If you can’t find this in the implementation guide, contact your trading partner. |
| date                     | The date this message was sent, e.g. `2022-08-15`.                                                                                                                                                                     |
| time                     | The time this message was sent, e.g. `13:20:52`.                                                                                                                                                                       |
| controlNumber            | A unique identifier for this functional group.                                                                                                                                                                         |

If you want to specify the transaction set envelope, you should do so in the JSON document, not in the `envelope` field.

Translate takes care of writing the transaction set trailer (SE segment) as long as the information for the transaction set header (ST) is provided in the JSON document. If the document does not include the transaction set header, translate will NOT write out the trailer.

At the moment, it isn’t possible to produce EDI documents with multiple functional groups.

## Delimiters

When writing EDI, EDI Translate uses the delimiters configured in the Stedi guide. When you create a guide, a default set of delimiters is assigned to the guide. You have the ability to change these values through the Stedi Guides UI. Just open your guide and click on _Overview_ in the top left. You’ll find the delimiters at the bottom.

## Calling the API

You translate a document by calling the API. First you need an [API key](https://www.stedi.com/app/settings/api-keys) and then you can call the EDI Translate endpoint: `https://edi-translate.us.stedi.com/2022-01-01/x12/from-json`.

```javascript
const axios = require("axios");

const apiKey = process.env.STEDI_API_KEY;

async function jsonToX12(guideId, document, envelope) {
  if (typeof document === "string") {
    document = JSON.parse(document);
  }

  const response = await axios({
    method: "post",
    url: "https://edi-translate.us.stedi.com/2022-01-01/x12/from-json",
    data: {
      guideId: guideId,
      input: document,
      envelope: envelope,
    },
    headers: { Authorization: `Key ${apiKey}` },
  });

  return response.data;
}
```
