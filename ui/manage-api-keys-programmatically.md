---
title: Manage API keys programmatically
slug: /ui/manage-api-keys-programmatically
description: You can manage your API keys using the Stedi API.
---

You can manage your API keys using the Stedi API. You will need an API key to do this, so you’ll have to create the first one manually, but after that, you can programatically create and delete keys.

- See also: [API Keys API reference](https://www.stedi.com/docs/api/apikeys)

- An API key looks like `alKOxcl.fbFprsgLiPNk1nbLwc9J04nk`. The part before the `.` is called the prefix and it serves as an ID for the API key.
- You can’t retrieve an API key after it’s been created. The response you get when creating an API key is the only time you receive the key.
- You can’t update an API key.

## List API keys

### Node.js with Axios

```javascript
const axios = require("axios");

const listApiKeysRequest = {
  method: "get",
  url: "https://apikeys.us.stedi.com/2022-01-19/api_keys",
  headers: {
    Authorization: `Key ${process.env.STEDI_API_KEY}`,
  },
};

const listApiKeysResponse = await axios(listApiKeysRequest);
```

### Node.js with Fetch

```javascript
const fetch = require("fetch");

const listApiKeysRequest = {
  method: "get",
  headers: {
    Authorization: `Key ${process.env.STEDI_API_KEY}`,
  },
};

const listApiKeysResponse = await fetch("https://apikeys.us.stedi.com/2022-01-19/api_keys", listApiKeysRequest);
```

### Curl

```bash
curl --request GET "https://apikeys.us.stedi.com/2022-01-19/api_keys" \
  --header "Authorization: Key ${STEDI_API_KEY}"
```

## Create an API key

### Node.js with Axios

```javascript
import axios from "axios";

const createApiKeyRequest = {
  method: "post",
  url: "https://apikeys.us.stedi.com/2022-01-19/api_keys",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Key ${process.env.STEDI_API_KEY}`,
  },
  data: {
    description: "alicia",
  },
};

const createApiKeyResponse = await axios(createApiKeyRequest);
console.info(createApiKeyResponse.data);
```

### Node.js with Fetch

```javascript
import fetch from "node-fetch";

const createApiKeyRequest = {
  method: "post",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Key ${process.env.STEDI_API_KEY}`,
  },
  body: JSON.stringify({
    description: "alicia",
  }),
};

const createApiKeyResponse = await fetch("https://apikeys.us.stedi.com/2022-01-19/api_keys", createApiKeyRequest);
console.info(await createApiKeyResponse.json());
```

### Curl

```bash
curl --request POST "https://apikeys.us.stedi.com/2022-01-19/api_keys" \
  --header 'Content-Type: application/json' \
  --header "Authorization: Key ${STEDI_API_KEY}" \
  --data-raw '{
      "description": "alicia"
  }'
```

## Delete an API key

### Node.js with Axios

```javascript
import axios from "axios";

const apiKeyPrefix = "alKOxcl";
const deleteApiKeyRequest = {
  method: "delete",
  url: `https://apikeys.us.stedi.com/2022-01-19/api_keys/${apiKeyPrefix}`,
  headers: {
    Authorization: `Key ${process.env.STEDI_API_KEY}`,
  },
};

await axios(deleteApiKeyRequest);
```

### Node.js with Fetch

```javascript
import fetch from "node-fetch";

const apiKeyPrefix = "alKOxcl";
const deleteApiKeyRequest = {
  method: "delete",
  headers: {
    Authorization: `Key ${process.env.STEDI_API_KEY}`,
  },
};

const deleteApiKeyResponse = await fetch(
  `https://apikeys.us.stedi.com/2022-01-19/api_keys/${apiKeyPrefix}`,
  deleteApiKeyRequest,
);
console.info(await deleteApiKeyResponse.json());
```

### Curl

```bash
curl --request DELETE "https://apikeys.us.stedi.com/2022-01-19/api_keys/alKOxcl" \
  --header "Authorization: Key ${STEDI_API_KEY}"
```
