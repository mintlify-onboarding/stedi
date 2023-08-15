---
title: Authentication
slug: /accounts-and-billing/authentication
tags:
  - gettingStarted
description: |
  How to create an API key and make authenticated API calls.
---

Requests to Stedi APIs are authenticated using a secret token called an _API key_. Each API key belongs to a single account and each account can have multiple API keys.

## Creating an API key

To create a new API key, [log into Stedi](https://www.stedi.com/auth), go to the navigation bar at the top of the page, click on the gear icon, and choose [_API Keys_](https://www.stedi.com/app/settings/api-keys). Then, click on the _Generate API key_ button.

You must provide a name for your API key. It can be anything you want, but typically you would use it either to describe the application that will use the key to call Stedi endpoints, like `ERP` or `Warehouse Management`, or to describe the environment in which the application will run, like `Production` or `Development`.

Your full API key will be displayed upon creation. This is the only time you're presented with the full API key, so be sure to copy the key and store it in a secure location.

> **Remember**: Your API key should be kept secret. Never share it or commit it to source control.

## Making authenticated API calls

When making an HTTP request to a Stedi API, pass your API key in the Authorization header, prefixed by the word `Key`. The following example shows a cURL request to [generate EDI](/docs/core/parsing-and-generating-edi#stedi-http-api). The transactions array is abbreviated for readability.

```bash
curl --location 'https://partners.us.stedi.com/2022-01-01/x12/partnerships/this-is-me_another-merchant/generate-edi' \
--header 'Content-Type: application/json'
--header 'Authorization: Key YOUR_STEDI_API_KEY_HERE' \
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
}
```

Be sure to replace `${STEDI_API_KEY}` with your actual API key.

The next example uses JavaScript and the Axios library to generate EDI instead.

```javascript
const axios = require("axios");

const apiKey = process.env.STEDI_API_KEY;

axios
  .request({
    url: "https://partners.us.stedi.com/2022-01-01/x12/partnerships/this-is-me_another-merchant/generate-edi",
    method: "POST",
    data: {
      filename: "my-output-file.edi",
      transactionGroups: [
        {
          transactionSettingId: "005010-850",
          transactions: [
            {
              heading: {},
              detail: {},
              summary: {},
            },
          ],
        },
      ],
    },
    headers: { Authorization: `Key ${process.env.STEDI_API_KEY}` },
  })
  .then((response) => {
    const guideJson = response.data.output;
    console.log(JSON.stringify(guideJson));
  })
  .catch((error) => {
    console.error(error);
  });
```

Authentication still happens the same way: by passing the API key in the Authorization header. However, the code doesn't include the API key directly. The API key needs to be kept secret, so you shouldn't commit it to source control. The example above reads it from an environment variable called `STEDI_API_KEY`.

## Recommended security practices

API keys allow you to access and modify data in your Stedi account, so it's important that untrusted parties are not able to access them.

- Don't share API keys. Use a different API key for each application that you build on Stedi and give each developer their own API key for testing.
- Don't commit API keys to source control. Instead, use environment variables, or use the configuration facility provided by the framework you use.
- Delete any API keys that are no longer used or that may have been exposed to an untrusted party.

## Limits

Each account may have up to 50 API keys at one time.
