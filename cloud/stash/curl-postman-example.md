---
title: curl/Postman Example
slug: cloud/stash/curl-postman-example
product: stash
description: ''
searchable: true
childPageOrder: []
---
## Example

This example highlights the common operations for the Stash service using [curl](https://curl.se/). These examples can also easily be imported into [Postman](https://www.postman.com/).

### Authentication

Ensure that you have created a [Stedi API Key](/docs/accounts-and-billing/authentication). If using curl, you can export this API key in the `STEDI_API_KEY` environment variable. If using Postman, the API key can be configured under the "Authorization" header with Type set to `API Key`, Key set to `Authorization` and Value set to `Key <your-stedi-api-key>`.

### Creating a Keyspace

In order to store keys and values, we need to create a Keyspace. We may use a single Keyspace for different
situations, as long as we ensure that the keys will not overwrite one another.

```sh
curl --request POST 'https://stash.us.stedi.com/2022-04-20/CreateKeyspace' \
    --header "Authorization: Key ${STEDI_API_KEY}" \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "keyspaceName": "my-keyspace"
    }'
```

*Note:* It may take a few seconds for a Keyspace to be ready to use. You can use the `/GetKeyspace` operation
to determine if it is in the `ACTIVE` state.

### Storing Values

This snippet just stores a simple item where the Key is `foo` and the Value is `bar`. Note how we use the same Keyspace name as used in the previous step.

```sh
curl --request POST 'https://stash.us.stedi.com/2022-04-20/SetValue' \
    --header "Authorization: Key ${STEDI_API_KEY}" \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "keyspaceName": "my-keyspace",
        "key": "foo",
        "value": "bar"
    }'
```

### Retrieving Values

Simply use the `GetValue` operation to retrieve any values.

```sh
curl --request POST 'https://stash.us.stedi.com/2022-04-20/GetValue' \
    --header "Authorization: Key ${STEDI_API_KEY}" \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "keyspaceName": "my-keyspace",
        "key": "foo"
    }'
```

### Atomically increment a number

The `IncrementValue` operation will atomically increment a number by the `amount` specified. If the key does not exist, it will be initialized with 0. If the key exists and is not a number, this results in a `ValidationError` error.

```sh
curl --request POST 'https://stash.us.stedi.com/2022-04-20/IncrementValue' \
    --header "Authorization: Key ${STEDI_API_KEY}" \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "keyspaceName": "my-keyspace",
        "key": "increasing-number",
        "amount": 1
    }'
```
