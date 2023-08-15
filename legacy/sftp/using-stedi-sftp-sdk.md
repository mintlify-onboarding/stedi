---
title: Using Stedi SFTP with the SDK
slug: legacy/sftp/using-stedi-sftp-sdk
product: sftp
lifecycle: legacy
description: ''
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

This example shows how to use the Stedi SFTP SDK. This is of use to JavaScript developers who want to generate SFTP
credentials programmatically, but don't want to interact with the Stedi SFTP API directly.

## Authentication

[Create an API key](/docs/accounts-and-billing/authentication) if you haven’t done so already. The examples on this page assume you have your API key stored in an environment variable called `STEDI_API_KEY`. You can do that with the following command.

```shell
export STEDI_API_KEY=replace.withYourOwnApiKey
```

## Install the SDK

The following code examples are in Javascript only.
We offer the Stedi SDK for JavaScript (we also provide TypeScript support). To get started, install the
[@stedi/sdk-client-sftp](https://www.npmjs.com/package/@stedi/sdk-client-sftp) package.
You can do this by running `npm install --save @stedi/sdk-client-sftp` in your local
directory. Please note that to run the following examples you need to install [node@16](https://nodejs.org/en/download/)
or higher.

## Create an SFTP user

Create a user. You can customize `description`, `homeDirectory` and `passwordOptions`.

*   `description` helps you find the user account later if you need to.
*   `homeDirectory` determines which files and directory the user has access to. The user can access any file or directory
    under their home directory, but none outside it.
*   `passwordOptions` determines how the password is generated.
    *   `length` determines the length of the password.

```javascript
const stediSftpSdk = require("@stedi/sdk-client-sftp");

const apiKey = process.env.STEDI_API_KEY; // Fetch your API key from an environment variable.

// Create a new SFTP Client for the Stedi US region
const stediSftpClient = new stediSftpSdk.SftpClient({
  region: "us",
  apiKey: apiKey,
});

// Create an SFTP user for Alice
const createUserOutput = await stediSftpClient.send(
  new stediSftpSdk.CreateUserCommand({
    description: "Trading partner Alice",
    homeDirectory: "/alice",
  }),
);

// Print the user object
console.log(createUserOutput);
```

The response will look like this.

```json
{
  "username": "WL9F11A9",
  "homeDirectory": "/alice",
  "description": "Trading partner Alice",
  "bucketName": "565460e4-4c6f-4a55-87f1-a9d27616f8aa-sftp",
  "endpoint": "transfer.us.stedi.com",
  "password": "k9TqUSvZsGPs9iav"
}
```

## Connect to the SFTP endpoint

Alice can use the returned credentials to connect to the SFTP endpoint using whichever SFTP client she prefers. We
provide an example using the command line SFTP client
here: [command line example](/docs/legacy/sftp/using-stedi-sftp-from-the-command-line)
