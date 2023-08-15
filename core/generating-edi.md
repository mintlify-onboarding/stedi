---
title: Generate EDI
slug: core/generating-edi
sidebarTitle: "Generate EDI"
product: core
description: "Generate and send X12 EDI by providing JSON to the Stedi Core API or SDK."
searchable: true
hidden: false
lifecycle: general_availability
---

Once a trading partner has been [configured](configuration) with an outbound transaction setting and connection, you can generate and send EDI files programmatically using the Core [API](#http-api) or [SDK](#stedi-sdk).

Each transaction that is generated successfully will appear in Core's [transaction data](transaction-data).

## Prerequisites

To generate EDI, you must [configure Core](configuration) for the trading partner.

## Outbound processing flow

Core generates and delivers EDI files according to the partnership's outbound transaction settings. This includes using the specified guide for validation and the associated [connection](configuration/connections) for file delivery.

When you use the API or SDK to generate EDI files, Core performs the following actions:

1. Validates the JSON against the configured guide, and returns an error if validation is not successful.
1. Generates an EDI document that conforms to the specified guide.
1. Persists the JSON payload and resulting EDI file.
1. Delivers the EDI file to the specified connection.
1. Displays the [transaction data](/docs/core/transaction-data).
1. Emits a `file.delivered` event and one or more `transaction.processed` [Events](/docs/events/event-types), which can be used for advanced processing.

## Runtime data required to generate EDI

To generate an EDI document, you submit a JSON payload to the Generate API. The Generate API accepts multiple arrays of transactions in a single API call, allowing you to send multiple transactions or even [multiple functionality groups](#advanced-edi-generation) in a single file.

The Generate API uses the following runtime data to generate and deliver an EDI file:

- The [partnership ID](configuration/profiles-and-partnerships) to identify the partnership.
- The [transaction setting ID](configuration/transaction-settings) to identify the outbound transaction setting. This is used to resolve the correct guide for validation and generation, as well as the connection to use for delivery.
- A JSON payload that conforms to the guide associated with the transaction setting.
- (Optional) An optional `filename` parameter that you can use to specify the name of the generated EDI file. This is useful if your trading partner has specific requirements for the file name. If you do not specify a filename, Core will generate a unique file name for you using a UUID.

### Format transaction payloads

To make a successful call to the Generate API, the transaction payload must conform to the JSON Schema defined by the specified guide for that outbound transaction setting. You can find the JSON Schema for a guide by following these steps:

1. Navigate to the [Trading partners page](configuration/profiles-and-partnerships).
1. Select the partnership for which you want to generate EDI.
1. Click the name of the guide associated with the outbound transaction setting.
1. Click **Actions** > **View Schema**.
1. **Important**: Select the **JSON to EDI** tab.

You can use this schema to ensure that the payload conforms to the guide before submitting it to the Generate API.

If you need a way to transform your internal format to Guide JSON, we recommend [Stedi Mappings](/docs/cloud/mappings). You can [create mappings based on the Stedi guide](/docs/core/guides/create-a-mapping-from-a-stedi-guide), call the Mappings API to transform JSON data into the required shape, and then submit the transformed JSON to the Generate API.

## Generate EDI

You can generate EDI with the HTTP API or the Stedi SDK.

### HTTP API

The following example shows a cURL request with the `transactions` array abbreviated for readability.

```shell
curl --location 'https://partners.us.stedi.com/2022-01-01/x12/partnerships/this-is-me_another-merchant/generate-edi' \
--header 'Content-Type: application/json' --header 'Authorization: Key YOUR_STEDI_API_KEY_HERE' \
--data-raw '{
  "filename": "my-output-file.edi",
  "transactionGroups": [
    {
      "transactionSettingId": "005010-850",
      "transactions": [
        {
          "heading": {},
          "detail": {},
          "summary": {}
        }
      ]
    }
  ]
}'
```

### Stedi SDK

For implementations using JavaScript, Stedi offers a JavaScript/TypeScript SDK that can run on any Node-based runtime, such as AWS Lambda, Google Cloud Functions, or Azure Functions.

The following example shows how to use the SDK to generate an X12 005010 850. The main `transactions` array is abbreviated for readability.

```javascript
import * as core from "@stedi/sdk-client-partners";

export const handler = async (event) => {
	const client = new core.PartnersClient({
		region: "us",
		apiKey: "YOUR_STEDI_API_KEY_HERE",
	});

	const { edi } = await client.send(
    new core.GenerateEdiCommand({
      partnershipId: "this-is-me_another-merchant",
      filename: "my-output-file.edi",
      transactionGroups: [
        {
          transactionSettingId: "005010-850",
          transactions: [
            {
              heading: {...},
              detail: {...},
              summary: {...}
            }
          ]
        }
      ],
    })
  );

  return { edi };
};
```

## View generated EDI

Both the SDK and API return the full X12 EDI file in the response. The response also includes an `artifactId` (equivalent to the file name) and a globally unique `fileExecutionID` that you can use to locate the generated file.

The following example shows a sample generated 850 Purchase Order.

```json
{
    "edi": "ISA*00*          *00*          *ZZ*THISISME       *14*ANOTHERMERCH   *230628*1159*U*00501*000000024*1*T*>~GS*PO*MINE*MYAPPID*20230628*115907*000000024*X*005010~ST*850*0001~BEG*00*DS*365465413**20220830~REF*CO*ACME-4567~REF*ZZ*Thank you for your business~PER*OC*Marvin Acme*TE*973-555-1212*EM*marvin@acme.com~TD5****ZZ*FHD~N1*ST*Wile E Coyote*92*123~N3*111 Canyon Court~N4*Phoenix*AZ*85001*US~PO1*item-1*0008*EA*400**VC*VND1234567*SK*ACM/8900-400~PID*F****400 pound anvil~PO1*item-2*0004*EA*125**VC*VND000111222*SK*ACM/1100-001~PID*F****Detonator~CTT*2~AMT*TT*3700~SE*16*0001~GE*1*000000024~IEA*1*000000024~",
    "artifactId": "my-output-file.edi",
    "fileExecutionId": "b70199fa-4a11-4610-861e-aa7fff4ae09b"
}
```

These generated files appear in the Core with the direction indicated as **↗ Sent**. Each Transaction within the generated file also appears on the **Transactions** page.

## 997 Functional Acknowledgments

Core can automatically generate 997 Functional Acknowledgments for every inbound transaction associated with a partnership, so you should not need to use the Generate API to send 997s. Visit [Profiles and partnerships](configuration/profiles-and-partnerships) for details on how to enable this functionality.

## Advanced EDI generation

The Generate API can automatically generate multiple [Functional Groups](https://www.stedi.com/edi/x12/segment/GS) in a single [Interchange](https://www.stedi.com/edi/x12/segment/ISA).

The Generate API groups transactions into the correct Functional Group, based on [Functional Identifier Code](https://www.stedi.com/edi/x12/element/479) for a given transaction set.
