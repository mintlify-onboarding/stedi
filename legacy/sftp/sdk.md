---
title: SFTP SDK
slug: legacy/sftp/sdk
product: sftp
description: "With the Stedi SFTP SDK you can programmatically access Stedi SFTP."
searchable: true
lifecycle: legacy
childPageOrder: []
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

You can access Stedi SFTP programmatically by creating an [`SftpClient`](#sftpclient) and using it to send [commands](#commands). You can use the Stedi SFTP SDK to manage SFTP users.

The SDK is available for JavaScript and works with TypeScript.

> For [accessing files programmatically](/docs/legacy/sftp/accessing-files), you should use the [Stedi Buckets SDK](/docs/sdk).

## Installation

To install the Stedi SFTP SDK, run the following command from your project directory.

```sh
npm install @stedi/sdk-client-sftp
```

## Commands

|                                         |                                     |
| --------------------------------------- | ----------------------------------- |
| [CreateUserCommand](#createusercommand) | Creates a new user.                 |
| [DeleteUserCommand](#deleteusercommand) | Deletes a user.                     |
| [ListUsersCommand](#listuserscommand)   | Lists all users.                    |
| [ReadUserCommand](#readusercommand)     | Retrieves information about a user. |
| [UpdateUserCommand](#updateusercommand) | Updates a user’s data.              |

### CreateUserCommand

Creates a new user.

If this is the first time you create a user, a bucket will be created to store all SFTP files. You can access the contents of the bucket using [Stedi Buckets](/docs/buckets/sdk).

#### Parameters

```javascript
{
  homeDirectory: string,
  description: description,
  passwordOptions: {
    length: number
  }
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                        |                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| homeDirectory          | Required. The absolute path to the home directory of the user. The user can access this directory and all its subdirectories, but no other directories. The path must start with a `/`. It can include letters and numbers, as well as the following characters: `-_:.+`. You can use `/` to specify subdirectories. For example, `/customers/paper-wrap/shipping`. |
| description            | Required. A string that describes the user. You can use it to identify the user, as the username will be generated for you. It can include letters and numbers, as well as the following characters: `-_ /`.                                                                                                                                                        |
| passwordOptions        | Optional. An object that specifies how to generate the user’s password.                                                                                                                                                                                                                                                                                             |
| passwordOptions.length | Required when passwordOptions is provided. The length of the password that will be generated. Minimum: 16. Maximum: 128. Default: 16.                                                                                                                                                                                                                               |

#### Return value

```javascript
{
  $metadata: object,
  username: string,
  password: string,
  homeDirectory: string,
  bucketName: string,
  description: string,
  endpoint: string
}
```

The [`send()`](#send) method of the [SFTP client](#sftpclient) will return the result of the command.

|               |                                                                                                                                                                                                                               |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata     | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                   |
| userName      | The generated name of the user. You can use this to log into the SFTP server as the user.                                                                                                                                     |
| password      | The generated password of the user. You can use this to log into SFTP server as the user. This is the only time the password is returned to you; there’s no way of retrieving it later.                                       |
| homeDirectory | The home directory of the user, as specified by you in the command.                                                                                                                                                           |
| bucketName    | The name of the bucket that holds the user’s files. You can use this in combination with the [Buckets SDK](/docs/sdk) to access the files programmatically. The bucket name is the same for all users within a Stedi account. |
| description   | The description of the user, as specified by you in the command.                                                                                                                                                              |
| endpoint      | The endpoint to the SFTP server. When you use an SFTP client to connect to the SFTP server, you can use this value as the address of the host.                                                                                |

#### Example

```javascript
import { SftpClient, CreateUserCommand } from "@stedi/sdk-client-sftp";

const homeDirectory = "YOUR HOME DIRECTORY HERE";
const description = "YOUR DESCRIPTION HERE";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const createUserCommand = new CreateUserCommand({
    homeDirectory: homeDirectory,
    description: description,
  });

  const createUserResult = await sftpClient.send(createUserCommand);
  console.info(createUserResult);
}

main();
```

### DeleteUserCommand

Deletes a user.

This will not delete the files in the user’s home directory. You should use the [`ListObjectCommand`](/docs/sdk#listobjectcommand) and [`DeleteObjectCommand`](/docs/sdk#deleteobjectcommand) in the Buckets SDK if you want to delete the user’s files.

#### Parameters

```javascript
{
  username: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|          |                                                                                                                                                                                                        |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| username | Required. The name of the user you want to delete. This is the name generated by Stedi SFTP and returned by the [CreateUserCommand](#createusercommand), not the description of the user you provided. |

#### Return value

```javascript
{
  $metadata: object;
}
```

The [`send()`](#send) method of the [SFTP client](#sftpclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |

#### Example

```javascript
import { SftpClient, DeleteUserCommand } from "@stedi/sdk-client-sftp";

const username = "YOUR USERNAME HERE";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteUserCommand = new DeleteUserCommand({
    username: username,
  });
  const deleteUserResult = await sftpClient.send(deleteUserCommand);
  console.info(`Deleted the user with username ${username}.`);
}

main();
```

### ListUsersCommand

Retrieve a list of all users.

#### Parameters

```javascript
{
  pageSize: number?,
  pageToken: string?
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the function, you must still provide an empty object.

|           |                                                                                                                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pageSize  | Optional. The maximum number of users you want to retrieve. Defaults to 25 and can’t be higher than 50.                                                                                 |
| pageToken | Optional. A token that allows you to retrieve the next page of results. It should have the same value as the `nextPageToken` field you received the last time you executed the command. |

#### Return value

```javascript
{
  $metadata: object,
  items: [{
    username: string,
    homeDirectory: string,
    description: string,
    bucketName: string,
    endpoint: string,
    lastConnectedDate: string
  }],
  nextPageToken: string?
}
```

The [`send()`](#send) method of the [SFTP client](#sftpclient) will return the result of the command.

|                         |                                                                                                                                                                                                                               |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata               | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                   |
| items                   | An array of users.                                                                                                                                                                                                            |
| items.bucketName        | The name of the bucket that holds the user’s files. You can use this in combination with the [Buckets SDK](/docs/sdk) to access the files programmatically. The bucket name is the same for all users within a Stedi account. |
| items.description       | The description of the user, as specified when sending the [CreateUserCommand](#createusercommand).                                                                                                                           |
| items.endpoint          | The endpoint to the SFTP server. When you use an SFTP client to connect to the SFTP server, you can use this value as the address of the host.                                                                                |
| items.homeDirectory     | The home directory of the user, as specified when sending the [CreateUserCommand](#createusercommand).                                                                                                                        |
| items.lastConnectedDate | The date the user last connected, e.g. `2022-07-25`. `undefined` if the user has never connected to the SFTP server.                                                                                                          |
| items.userName          | The generated name of the user. You can use this to log into the SFTP server as the user.                                                                                                                                     |

#### Example

```javascript
import { SftpClient, ListUsersCommand } from "@stedi/sdk-client-sftp";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listUsersCommand = new ListUsersCommand({
      pageSize: 7,
      pageToken: pageToken,
    });

    const listUsersResult = await sftpClient.send(listUsersCommand);
    console.info(listUsersResult.items);

    pageToken = listUsersResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ReadUserCommand

Retrieves information about a user.

#### Parameters

```javascript
{
  username: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|          |                                                                                                                                                                                                          |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| username | Required. The name of the user you want to retrieve. This is the name generated by Stedi SFTP and returned by the [CreateUserCommand](#createusercommand), not the description of the user you provided. |

#### Return value

```javascript
{
  $metadata: object,
  bucketName: string,
  description: string,
  endpoint: string,
  homeDirectory: string,
  lastConnectedDate: string,
  userName: string
}
```

The [`send()`](#send) method of the [SFTP client](#sftpclient) will return the result of the command.

|                   |                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata         | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                   |
| bucketName        | The name of the bucket that holds the user’s files. You can use this in combination with the [Buckets SDK](/docs/sdk) to access the files programmatically. The bucket name is the same for all users within a Stedi account. |
| description       | The description of the user, as specified when sending the [CreateUserCommand](#createusercommand).                                                                                                                           |
| endpoint          | The endpoint to the SFTP server. When you use an SFTP client to connect to the SFTP server, you can use this value as the address of the host.                                                                                |
| homeDirectory     | The home directory of the user, as specified when sending the [CreateUserCommand](#createusercommand).                                                                                                                        |
| lastConnectedDate | The date when the user last connected, e.g. `2022-07-25`. `undefined` if the user has never connected to the SFTP server.                                                                                                     |
| userName          | The generated name of the user. You can use this to log into the SFTP server as the user.                                                                                                                                     |

#### Example

```javascript
import { SftpClient, ReadUserCommand } from "@stedi/sdk-client-sftp";

const username = "YOUR USER NAME HERE";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const readUserCommand = new ReadUserCommand({
    username: username,
  });
  const readUserResult = await sftpClient.send(readUserCommand);
  console.info(readUserResult);
}

main();
```

### UpdateUserCommand

Updates a user’s data.

#### Parameters

```javascript
{
  username: string,
  description: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|             |                                                                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| username    | Required. The name of the user you want to update. This is the name generated by Stedi SFTP and returned by the [CreateUserCommand](#createusercommand).          |
| description | Required. A string that describes the user. You can use it to identify the user. It can include letters and numbers, as well as the following characters: `-_ /`. |

#### Return value

```javascript
{
  $metadata: object,
  bucketName: string,
  description: string,
  endpoint: string,
  homeDirectory: string,
  lastConnectedDate: string,
  userName: string
}
```

The [`send()`](#send) method of the [SFTP client](#sftpclient) will return the result of the command.

|                   |                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata         | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                   |
| bucketName        | The name of the bucket that holds the user’s files. You can use this in combination with the [Buckets SDK](/docs/sdk) to access the files programmatically. The bucket name is the same for all users within a Stedi account. |
| description       | The description of the user, as specified in the command.                                                                                                                                                                     |
| endpoint          | The endpoint to the SFTP server. When you use an SFTP client to connect to the SFTP server, you can use this value as the address of the host.                                                                                |
| homeDirectory     | The home directory of the user, as specified when sending the [CreateUserCommand](#createusercommand).                                                                                                                        |
| lastConnectedDate | The date the user last connected, e.g. `2022-07-25`. `undefined` if the user has never connected to the SFTP server.                                                                                                          |
| userName          | The generated name of the user. You can use this to log into the SFTP server as the user.                                                                                                                                     |

#### Example

```javascript
import { SftpClient, UpdateUserCommand } from "@stedi/sdk-client-sftp";

const username = "YOUR USER NAME HERE";
const description = "YOUR DESCRIPTION HERE";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const updateUserCommand = new UpdateUserCommand({
    username: username,
    description: description,
  });
  const updateUserResult = await sftpClient.send(updateUserCommand);
  console.info(updateUserResult);
}

main();
```

## SftpClient

|                             |                                                                  |
| --------------------------- | ---------------------------------------------------------------- |
| [constructor](#constructor) | Creates a client that allows you to send commands to Stedi SFTP. |
| [destroy()](#destroy)       | Shuts down the client and its underlying resources.              |
| [send()](#send)             | Sends a command.                                                 |

### constructor

Creates a client that allows you to send commands to Stedi SFTP.

#### Parameters

```javascript
{
  region: string,
  apiKey: string
}
```

The constructor takes an object as its only argument. All parameters are fields within this object.

|        |                                                                                                                                                                                                                                       |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| region | Required. The region that hosts your SFTP resources. At the moment, we only support `us`.                                                                                                                                             |
| apiKey | Required. The [API key](https://www.stedi.com/docs/accounts-and-billing/authentication) used to authenticate your requests. You can create a client without specifying `apiKey`, but if you do, calls to [`send()`](#send) will fail. |

#### Return value

An instance of the `SftpClient` class.

#### Exceptions

|       |                      |
| ----- | -------------------- |
| Error | `region` is missing. |

#### Example

```javascript
import { SftpClient } from "@stedi/sdk-client-sftp";

const sftpClient = new SftpClient({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});
```

### destroy

Shuts down the client and its underlying resources.

The client shuts down automatically when it’s garbage collected, but in a long-running process, it’s best to explicitly shut it down when it’s no longer needed.

#### Return value

None.

#### Exceptions

None.

#### Example

```javascript
import { SftpClient } from "@stedi/sdk-client-sftp";

const sftpClient = new SftpClient({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});

sftpClient.destroy();
```

### send

Sends a command to Stedi SFTP.

#### Parameters

```javascript
{
  command: Command;
}
```

`command` must be an instance of one of the [valid SFTP commands](#commands).

#### Return value

Depends on the command you sent. Check the description of the command you’re interested in.

#### Exceptions

Depends on the command you sent. Check the description of the command you’re interested in.

#### Example

```javascript
import { SftpClient } from "@stedi/sdk-client-sftp";

const username = "YOUR USERNAME HERE";

async function main() {
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const readUserCommand = new ReadUserCommand({
      username: username,
    });
    const readUserResult = await sftpClient.send(readUserCommand);
    console.info(`Last connected at ${readUserResult.lastConnectedDate}.`);
  } catch (error) {
    console.info("The user doesn’t exist.");
  }
}

main();
```
