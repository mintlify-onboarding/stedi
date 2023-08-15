---
title: JavaScript Example
slug: cloud/stash/javascript-example
product: stash
description: ''
searchable: true
childPageOrder: []
---
## Example

This example highlights the common operations for the Stash service using the Stedi SDK for JavaScript.

### Setup

Install the SDK with `npm install -g @stedi/sdk-client-stash`. Ensure you have created a [Stedi API Key](/docs/accounts-and-billing/authentication).

```javascript
const { StashClient } = require("@stedi/sdk-client-stash");

const client = new StashClient({
  region: "us",
  apiKey: "<replace with Stedi API key>",
});
```

### Creating a Keyspace

In order to store keys and values, we need to create a Keyspace. We may use a single Keyspace for different
situations, as long as we ensure that the keys will not overwrite one another.

```javascript
const { CreateKeyspaceCommand } = require("@stedi/sdk-client-stash");

try {
  const output = await client.send(
    new CreateKeyspaceCommand({
      keyspaceName: "my-keyspace",
    })
  );
  console.log(`Keyspace status is ${output.status}`);
} catch (e) {
  throw e;
}
```

*Note:* It may take a few seconds for a Keyspace to be ready to use. You can use the `GetKeyspace` operation
to determine if it is in the `ACTIVE` state.

### Storing Values

This snippet just stores a simple item where the Key is `foo` and the Value is `bar`. Note how we use the same Keyspace name as used in the previous step.

```javascript
const { SetValueCommand } = require("@stedi/sdk-client-stash");

  const rl = readline.createInterface({
    input: fileStream,
    crlfDelay: Infinity,
  });

  for await (const line of rl) {
    item = JSON.parse(line);
    const url = baseUrl + "/SetValue";
    const payload = {
      keyspace: keyspaceName,
      key: `${item.vendor_id}|${item.vendor_sku}`,
      value: item.internal_sku,
    };
    await axios
      .post(url, payload, config)
      .then((resp) => {
        console.log(`Stored key=${payload.key}, value=${payload.value}`);
      })
      .catch((e) => {
        console.log(`Error setting value: ${e}`);
        process.exit(1);
      });
  }
}
```

### Retrieving Values

Simply use the `GetValue` operation to retrieve any values.

```javascript
const { GetValueCommand } = require("@stedi/sdk-client-stash");

try {
  const output = await client.send(
    new GetValueCommand({
      keyspaceName: "my-keyspace",
      key: "foo",
    })
  );
  console.log(`Retrieved key=${output.key}, value=${output.value}`);
} catch (e) {
  console.error(e);
}
```

### Incrementing Numbers

The `IncrementValue` operation will atomically increment a number by the `amount` specified. If the key does not exist, it will be initialized with 0. If the key exists and is not a number, this results in a `ValidationError` error.

```javascript
const { IncrementValueCommand } = require("@stedi/sdk-client-stash");

try {
  const output = await client.send(
    new IncrementValueCommand({
      keyspaceName: "my-keyspace",
      key: "invoice-number",
      amount: 1,
    })
  );
  console.log(`Incremented key=${output.key}, value=${output.value}`);
} catch (e) {
  console.error(e);
}
```
