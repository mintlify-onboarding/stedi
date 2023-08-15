---
title: Using an API key in your code
slug: /ui/using-an-api-key-in-your-code
description: When you call a Stedi API, you need to pass your API key in the Authorization header.
---

When you call a Stedi API, you need to pass your API key in the Authorization header. If you use the Stedi SDK, it will make the call for you, so you need to tell it which API key to use.

```http
POST https://buckets.cloud.us.stedi.com/2022-05-05/buckets
Content-Type: application/json
Authorization: Key alKOxcl.fbFprsgLiPNk1nbLwc9J04nk

{
  "bucketName": "documents"
}
```

You need to keep your API key safe, because anyone who has it, can access all the resources in your account. You shouldn’t hardcode the API key into your code. Instead, we recommend that you store it in an environment variable called `STEDI_API_KEY`.

- You can read the environment variable from your code.
- The Stedi CLI will use the API key in `STEDI_API_KEY`.
- You can pass environment variables to Stedi Functions when you create a function.

## Node.js with the Stedi SDK

```javascript
import { BucketsClient, ListBucketsCommand } from "@stedi/sdk-client-buckets";

const bucketsClient = new BucketsClient({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});

const listBucketsCommand = new ListBucketsCommand({});
const listBucketsResult = await bucketsClient.send(listBucketsCommand);
console.info(listBucketsResult.items);
```

## Node.js with Axios

```javascript
import axios from "axios";

const listBucketsRequest = {
  method: "get",
  url: "https://buckets.cloud.us.stedi.com/2022-05-05/buckets",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Key ${process.env.STEDI_API_KEY}`,
  },
};

const listBucketsResponse = await axios(listBucketsRequest);
console.info(listBucketsResponse.data.items);
```

## Node.js with Fetch

```javascript
import fetch from "node-fetch";

const listBucketsRequest = {
  method: "get",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Key ${process.env.STEDI_API_KEY}`,
  },
};

const listBucketsResponse = await fetch("https://buckets.cloud.us.stedi.com/2022-05-05/buckets", listBucketsRequest);
console.info(await listBucketsResponse.json());
```

## Curl

```console
curl --request GET "https://buckets.cloud.us.stedi.com/2022-05-05/buckets" \
  --header 'Content-Type: application/json' \
  --header "Authorization: Key ${STEDI_API_KEY}"
```
