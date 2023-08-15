---
title: Using Stedi SFTP from the command line
slug: legacy/sftp/using-stedi-sftp-from-the-command-line
lifecycle: legacy
product: sftp
description: ''
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

This example shows how to use the SFTP service from the command line.

## Authentication

[Create an API key](/docs/accounts-and-billing/authentication) if you haven’t done so already. The examples on this page assume you have your API key stored in an environment variable called `STEDI_API_KEY`. You can do that with the following command.

```shell
export STEDI_API_KEY=replace.withYourOwnApiKey
```

## Create an SFTP user

Create a user by calling the Stedi SFTP API. You can customize `description`, `homeDirectory` and `passwordOptions`.

*   `description` helps you find the user account later if you need to.
*   `homeDirectory` determines which files and directory the user has access to. The user can access any file or directory
    under their home directory, but none outside of it.
*   `passwordOptions` determines how the password is generated.
    *   `length` determines the length of the password.

```shell
curl --request POST 'https://api.sftp.us.stedi.com/2022-04-01/users' \
    --header "Authorization: Key ${STEDI_API_KEY}" \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "homeDirectory": "/directory/for/partner1",
        "description": "Trading Partner 1"
    }'
```

The response will look something like this.

```json
{
  "username": "WL9F11A9",
  "homeDirectory": "/directory/for/partner1",
  "description": "Trading Partner 1",
  "bucketName": "565460e4-4c6f-4a55-87f1-a9d27616f8aa-sftp",
  "endpoint": "transfer.us.stedi.com",
  "password": "k9TqUSvZsGPs9iav"
}
```

## Connect to the SFTP endpoint

You can use the returned credentials to connect to the SFTP endpoint. You can use whichever SFTP client you prefer. In this example, we’ll access the endpoint from the command line.

```shell
sftp WL9F11A9@transfer.us.stedi.com
```

The client will ask for your password. After that, you will see the following prompt.

```
sftp>
```

You can type in commands to interact with the SFTP endpoint. If you’re not familiar with SFTP commands, here are a few common ones to get you started.

| Command  | Description                                                                          |
| -------- | ------------------------------------------------------------------------------------ |
| `pwd`    | Show the name of the active directory on the SFTP endpoint.                          |
| `lpwd`   | Show the name of the active directory on your local machine.                         |
| `ls`     | List the contents of the active directory on the SFTP endpoint.                      |
| `lls`    | List the contents of the active directory on your local machine.                     |
| `cd`     | Change the active directory on the SFTP endpoint, e.g. `cd documents`.               |
| `lcd`    | Change the active directory on your local machine, e.g. `cd documents`.              |
| `mkdir`  | Create a directory on the SFTP endpoint, e.g. `mkdir documents`.                     |
| `get`    | Download a file from the SFTP endpoint to your local machine, e.g. `get file.txt`.   |
| `put`    | Upload a file from your local machine to the SFTP endpoint, e.g. `put file.txt`.     |
| `rename` | Change the name of a file on the SFTP endpoint, e.g. `rename file.txt document.txt`. |
| `rm`     | Remove a file from the SFTP endpoint, e.g. `rm document.txt`.                        |
| `bye`    | Disconnect from the SFTP endpoint.                                                   |
