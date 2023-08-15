---
title: What’s an API key
slug: /ui/whats-an-api-key
description: An API key allows you to authenticate yourself when your code makes a call to a Stedi API.
---

An API key allows you to authenticate yourself when your code makes a call to a Stedi API. It tells the API which resources you have access to.

You send an API key to the Stedi API by using the Authorization header.

```http
POST https://buckets.cloud.us.stedi.com/2022-05-05/buckets
Content-Type: application/json
Authorization: Key alKOxcl.fbFprsgLiPNk1nbLwc9J04nk

{
  "bucketName": "documents"
}
```

- See also: [Using an API key in your code](/docs/ui/using-an-api-key-in-your-code)

Anyone who has your API key, can access all the resources in your account.

- Create separate API keys for anyone who needs access, rather than sharing a single API key. It makes it easier to revoke access when necessary.
- Never commit your API key to source control.
- Don’t call the Stedi API directly from a browser or a mobile app.

The description you give to an API key is not used by the Stedi API in any way. You can use it to keep track of who you gave API keys to, and to revoke a key when you need to.