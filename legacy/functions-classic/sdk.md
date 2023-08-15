---
title: Functions SDK
slug: legacy/functions-classic/sdk
product: functions-classic
description: ""
searchable: false
---

You can access Stedi Functions programmatically by creating a [FunctionsClient](#functionsclient)-object and using it to send commands.

The SDK is available for JavaScript and works with TypeScript.

> If you want to configure [bucket notifications](/docs/buckets/notifications) that automatically invoke a function, you should use the [Stedi Buckets SDK](/docs/buckets/sdk#updatebucketcommand).

## Installation

To install the Stedi Functions SDK, run the following command from your project directory.

```sh
npm install @stedi/sdk-client-functions@0.0.x
```

## Commands

|                                                 |                                        |
| ----------------------------------------------- | -------------------------------------- |
| [CreateFunctionCommand](#createfunctioncommand) | Create a function.                     |
| [DeleteFunctionCommand](#deletefunctioncommand) | Delete a function.                     |
| [InvokeFunctionCommand](#invokefunctioncommand) | Invoke a function.                     |
| [ListFunctionsCommand](#listfunctionscommand)   | List all functions in your account.    |
| [ReadFunctionCommand](#readfunctioncommand)     | Retrieve information about a function. |
| [UpdateFunctionCommand](#updatefunctioncommand) | Replace a function.                    |

### CreateFunctionCommand

Create a function.

#### Parameters

```javascript
{
  functionName: string,
  code: string,
  package: Uint8Array,
  environmentVariables: object,
  logRetention: string,
  timeout: number
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| functionName         | Required. The name of the function you want to update. The function name must be unique within your account. It may only contain letters, numbers, dashes, and underscores. Maximum length: 64 characters.                                                                                                                                                                                                                                                                          |
| code                 | `code` and `package` are mutually exclusive and one of them must be set. The code of the function as a string. Maximum size: 4 KB.                                                                                                                                                                                                                                                                                                                                                  |
| package              | `code` and `package` are mutually exclusive and one of them must be set. The code of the function, as a zip package. The zip package must contain a file called `index.mjs` and that file must export a function called `handler`. The function should be async. The package may contain a `node_modules` folder with dependencies, although we recommend [bundling all code into a single file](https://github.com/Stedi-Demos/deploy-functions-with-the-sdk). Maximum size: 3 MB. |
| environmentVariables | Optional. The environment variables that will be passed to the function when it’s invoked. You should not store secrets in environment variables; use [Stash](/docs/stash) instead. The only exception is passing the Stedi API key as an environment variable, because you need it to access Stash. Maximum size (for all environment variables combined): 4 KB. Default: `{}`.                                                                                                    |
| logRetention         | Optional. Indicates how long the function’s logs will be stored. Valid values are: `one_day`, `three_days`, `five_days`, `one_week`, `two_weeks`, `one_month`, `two_months`, `three_months`, `four_months`, `five_months`, `six_months`, `one_year`, `thirteen_months`, `eighteen_months`, `two_years`, `five_years`, and `ten_years`. Default: `one_day`.                                                                                                                          |
| timeout              | Optional. The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes. Default: `3`. Minimum: `1`, Maximum: `900`.                                                                                                                                                                                                                                                             |

#### Return value

```javascript
{
  $metadata: object,
  code: string,
  createdAt: string,
  environmentVariables: object,
  functionName: string,
  logRetention: string,
  timeout: number,
  updatedAt: string
}
```

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|                      |                                                                                                                                                                   |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.       |
| code                 | The code of the function. This will only be present if you created the code using a string, not a package.                                                        |
| createdAt            | The date and time at which the bucket was created, e.g. `2022-11-01T18:34:52.196Z`.                                                                               |
| environmentVariables | The environment variables that will be passed to the function when it’s invoked.                                                                                  |
| functionName         | The name of the function.                                                                                                                                         |
| logRetention         | Indicates how long the function’s logs will be stored.                                                                                                            |
| timeout              | The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes. |
| updatedAt            | The date and time at which the bucket was last updated, e.g. `2022-11-02T13:40:32.562Z`. Since you just created the function, this is the same as `createdAt`.    |

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

[TODO]: # "More exception descriptions"

#### Example

Provide code as a string.

```javascript
import {
  FunctionsClient,
  CreateFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";
const code =
  "exports.handler = async function(event) {" + "  return `It's ${event.username}. ${process.env.GREETING}!`;" + "}";

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const createFunctionCommand = new CreateFunctionCommand({
      functionName: functionName,
      code: code,
      logRetention: "three_days",
      timeout: 10,
      environmentVariables: {
        GREETING: "Hello there",
      },
    });
    const createFunctionResult = await functionsClient.send(createFunctionCommand);
    console.info(createFunctionResult);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

Provide code as a package.

```javascript
import {
  FunctionsClient,
  CreateFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";
import JSZip from "jszip";

const functionName = "YOUR FUNCTION NAME HERE";
const code =
  "export async function handler(event) { " + "  return `${process.env.GREETING}, ${event.username}!`;" + "}";

async function main() {
  const zip = new JSZip();
  zip.file("index.mjs", code);
  const packagedCode = await zip.generateAsync({
    type: "uint8array",
    compression: "DEFLATE",
  });

  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const createFunctionCommand = new CreateFunctionCommand({
      functionName: functionName,
      package: packagedCode,
      logRetention: "three_days",
      timeout: 10,
      environmentVariables: {
        GREETING: "Hello again",
      },
    });
    const createFunctionResult = await functionsClient.send(createFunctionCommand);
    console.info(createFunctionResult);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

### DeleteFunctionCommand

Delete a function.

#### Parameters

```javascript
{
  functionName: string;
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|              |                                              |
| ------------ | -------------------------------------------- |
| functionName | The name of the function you want to delete. |

#### Return value

```javascript
{
  $metadata: object;
}
```

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

#### Example

```javascript
import {
  FunctionsClient,
  DeleteFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const deleteFunctionCommand = new DeleteFunctionCommand({
      functionName: functionName,
    });
    await functionsClient.send(deleteFunctionCommand);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

### InvokeFunctionCommand

Invoke a function.

#### Parameters

```javascript
{
  functionName: string,
  requestPayload: Uint8Array
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|                |                                                                                                                                                           |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| functionName   | Required. The name of the function you want to invoke.                                                                                                    |
| requestPayload | Optional. The data you want to send to the function as its `event` parameter. The payload should be a buffer created from valid JSON. Maximum size: 4 MB. |

#### Return value

```javascript
{
  $metadata: object,
  functionName: string,
  responsePayload: Uint8Array,
  invocationLogs: string,
  invocationError: string
}
```

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|                 |                                                                                                                                                             |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata       | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |
| functionName    | The name of the function. This is the same as the name you specified.                                                                                       |
| responsePayload | A byte buffer with the return value of the function.                                                                                                        |
| invocationLogs  | The first few bytes of the function’s logs, Base64-encoded.                                                                                                 |
| invocationError | Indicates if the function failed, either due to a runtime error, or because it timed out. `"Unhandled"` if it did, `undefined` otherwise.                   |

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

[TODO]: # "More exception descriptions"

#### Example

```javascript
import {
  FunctionsClient,
  InvokeFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";
const input = {
  username: "stranger",
};

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const invokeFunctionCommand = new InvokeFunctionCommand({
      functionName: functionName,
      requestPayload: Buffer.from(JSON.stringify(input)),
    });
    const invokeFunctionResult = await functionsClient.send(invokeFunctionCommand);

    const responsePayload = Buffer.from(invokeFunctionResult.responsePayload).toString();
    console.info(responsePayload);

    const invocationLogs = Buffer.from(invokeFunctionResult.invocationLogs, "base64").toString();
    console.info(invocationLogs);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

### ListFunctionsCommand

List all functions in your account.

#### Parameters

```javascript
{
  pageSize: number,
  pageToken: string
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|           |                                                                                                                                                                                                    |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| pageSize  | Optional. The maximum number of functions you want to retrieve. Defaults to 25 and can’t be higher than 50.                                                                                        |
| pageToken | Optional. A token that allows you to retrieve the next page of results. It should have the same value as the `nextPageToken` field you received the last time you sent the `ListFunctionsCommand`. |

#### Return value

```javascript
{
  $metadata: object,
  items: {
    createdAt: string,
    code: string,
    environmentVariables: object,
    functionName: string,
    logRetention: string,
    timeout: number,
    updatedAt: string
  }
}
```

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|                      |                                                                                                                                                                   |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.       |
| createdAt            | The date and time at which the bucket was created, e.g. `2022-11-01T18:34:52.196Z`.                                                                               |
| environmentVariables | The environment variables that will be passed to the function when it’s invoked. These are specified when the function is [created](#createfunctioncommand).      |
| functionName         | The name of the function. This is the same as the name you specified.                                                                                             |
| logRetention         | Indicates how long the function’s logs will be stored.                                                                                                            |
| timeout              | The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes. |
| updatedAt            | The date and time at which the bucket was last updated, e.g. `2022-11-02T13:40:32.562Z`.                                                                          |

[TODO]: # "Add descriptions for operationStatusCode and operationToken."

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

[TODO]: # "More exception descriptions"

#### Example

```javascript
import { FunctionsClient, ListFunctionsCommand } from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";
const input = {
  username: "stranger",
};

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listFunctionsCommand = new ListFunctionsCommand({
      pageSize: 17,
      pageToken: pageToken,
    });
    const listFunctionsResult = await functionsClient.send(listFunctionsCommand);
    console.info(listFunctionsResult.items);

    pageToken = listFunctionsResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ReadFunctionCommand

Retrieve information about a function.

#### Parameters

```javascript
{
  functionName: string;
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|              |                                                                |
| ------------ | -------------------------------------------------------------- |
| functionName | Required. The name of the function you want information about. |

#### Return value

```javascript
{
  $metadata: object,
  code: string,
  createdAt: string,
  environmentVariables: object,
  functionName: string,
  logRetention: string,
  timeout: number,
  updatedAt: string
}
```

[TODO]: # "Add `code`."

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|                      |                                                                                                                                                                             |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                 |
| code                 | The code of the function. This will only be present if you [created](#createfunctioncommand) (or [updated](#updatefunctioncommand)) the code using a string, not a package. |
| createdAt            | The date and time at which the bucket was created, e.g. `2022-11-01T18:34:52.196Z`.                                                                                         |
| environmentVariables | The environment variables that will be passed to the function when it’s invoked. These are specified when the function is [created](#createfunctioncommand).                |
| functionName         | The name of the function. This is the same as the name you specified.                                                                                                       |
| logRetention         | Indicates how long the function’s logs will be stored.                                                                                                                      |
| timeout              | The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes.           |
| updatedAt            | The date and time at which the bucket was last updated, e.g. `2022-11-02T13:40:32.562Z`.                                                                                    |

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

#### Example

```javascript
import {
  FunctionsClient,
  ReadFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const readFunctionCommand = new ReadFunctionCommand({
      functionName: functionName,
    });
    const readFunctionResult = await functionsClient.send(readFunctionCommand);
    console.info(readFunctionResult);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

### UpdateFunctionCommand

Replace a function.

The function will be completely replaced with the updated settings. The settings you don’t specify will be replaced with their default values.

#### Parameters

```javascript
{
  functionName: string,
  code: string,
  package: Uint8Array,
  environmentVariables: object,
  logRetention: string,
  timeout: number
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| functionName         | Required. The name of the function you want to update.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| code                 | `code` and `package` are mutually exclusive and one of them must be set. The code of the function as a string. This will replace the code the function currently has, regardless of whether that code was previously provided as a string or a package. Maximum size: 4 KB.                                                                                                                                                                                                                                                                                                                                                  |
| package              | `code` and `package` are mutually exclusive and one of them must be set. The code of the function, as a zip package. This will replace the code the function currently has, regardless of whether that code was previously provided as a string or a package. The zip package must contain a file called `index.mjs` and that file must export a function called `handler`. The function should be async. The package may contain a `node_modules` folder with dependencies, although we recommend [bundling all code into a single file](https://github.com/Stedi-Demos/deploy-functions-with-the-sdk). Maximum size: 3 MB. |
| environmentVariables | Optional. The environment variables that will be passed to the function when it’s invoked. You should not store secrets in environment variables; use [Stash](/docs/stash) instead. The only exception is passing the Stedi API key as an environment variable, because you need it to access Stash. Maximum size (for all environment variables combined): 4 KB. Default: `{}`.                                                                                                                                                                                                                                             |
| logRetention         | Optional. Indicates how long the function’s logs will be stored. Valid values are: `one_day`, `three_days`, `five_days`, `one_week`, `two_weeks`, `one_month`, `two_months`, `three_months`, `four_months`, `five_months`, `six_months`, `one_year`, `thirteen_months`, `eighteen_months`, `two_years`, `five_years`, and `ten_years`. Default: `one_day`.                                                                                                                                                                                                                                                                   |
| timeout              | Optional. The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes. Default: `3`. Minimum: `1`, Maximum: `900`.                                                                                                                                                                                                                                                                                                                                                                                                      |

[TODO]: # "Add link to article explaining how to create a package."

#### Return value

```javascript
{
  $metadata: object,
  code: string,
  createdAt: string,
  environmentVariables: object,
  functionName: string,
  logRetention: string,
  timeout: number,
  updatedAt: string
}
```

The [`send()`](#send) method of the [Functions client](#functionsclient) will return the result of the command.

|                      |                                                                                                                                                                   |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.       |
| code                 | The code of the function. This will only be present if you updated the code using a string, not a package.                                                        |
| createdAt            | The date and time at which the bucket was created, e.g. `2022-11-01T18:34:52.196Z`.                                                                               |
| environmentVariables | The environment variables that will be passed to the function when it’s invoked. These are specified when the function is [created](#createfunctioncommand).      |
| functionName         | The name of the function. This is the same as the name you specified.                                                                                             |
| logRetention         | Indicates how long the function’s logs will be stored.                                                                                                            |
| timeout              | The maximum number of seconds a single invocation of the function is allowed to last. If the function runs for longer, it will be terminated before it completes. |
| updatedAt            | The date and time at which the bucket was last updated, e.g. `2022-11-02T13:40:32.562Z`.                                                                          |

#### Exceptions

The [`send()`](#send) method of the [Functions client](#functionsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                            |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The function name you specified isn’t valid. The function name may only contain letters, numbers, dashes, and underscores. |
| ResourceNotFoundException | A function with the name you specified doesn’t exist in your account.                                                      |

[TODO]: # "More exception descriptions"
[TODO]: # "missing code/package: InternalFailureException: number of failed tasks 1 exceeded tolerance for failed tasks 0.Debug: resource Update AWS::S3::Package started for Deployment"
[TODO]: # "invalid logRetention: ValidationException"
[TODO]: # "invalid timeout: ValidationException"
[TODO]: # "invalid zip package, at least one non-empty file: BadRequestException: invalid function code or configuration parameters"

#### Example

Provide code as a string.

```javascript
import {
  FunctionsClient,
  UpdateFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";
const code =
  "exports.handler = async function(event) {" + "  return `${process.env.GREETING}, ${event.username}!`;" + "}";

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const updateFunctionCommand = new UpdateFunctionCommand({
      functionName: functionName,
      code: code,
      logRetention: "three_days",
      timeout: 10,
      environmentVariables: {
        GREETING: "Hello there",
      },
    });
    const updateFunctionResult = await functionsClient.send(updateFunctionCommand);
    console.info(updateFunctionResult);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

Provide as a package.

```javascript
import {
  FunctionsClient,
  UpdateFunctionCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-functions";
import JSZip from "jszip";

const functionName = "YOUR FUNCTION NAME HERE";
const code =
  "export async function handler(event) { " + "  return `${process.env.GREETING}, ${event.username}!`;" + "}";

async function main() {
  const zip = new JSZip();
  zip.file("index.mjs", code);
  const packagedCode = await zip.generateAsync({
    type: "uint8array",
    compression: "DEFLATE",
  });

  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const updateFunctionCommand = new UpdateFunctionCommand({
      functionName: functionName,
      package: packagedCode,
      logRetention: "three_days",
      timeout: 10,
      environmentVariables: {
        GREETING: "Hello again",
      },
    });
    const updateFunctionResult = await functionsClient.send(updateFunctionCommand);
    console.info(updateFunctionResult);
  } catch (exception) {
    if (exception instanceof ValidationException) {
      console.info(`${functionName} is not a valid name for a function.`);
    } else if (exception instanceof ResourceNotFoundException) {
      console.info(`Function ${functionName} doesn’t exist.`);
    } else {
      throw exception;
    }
  }
}

main();
```

## FunctionsClient

|                             |                                                                       |
| --------------------------- | --------------------------------------------------------------------- |
| [constructor](#constructor) | Creates a client that allows you to send commands to Stedi Functions. |
| [destroy()](#destroy)       | Shuts down the client and its underlying resources.                   |
| [send()](#send)             | Sends a command.                                                      |

### constructor

Creates a client that allows you to send commands to Stedi Functions.

#### Parameters

```javascript
{
  region: string,
  apiKey: string
}
```

The constructor takes an object as its only argument. All parameters are fields within this object.

|        |                                                                                                                                                                                                                    |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| region | Required. The region that hosts the functions you want to connect to. At the moment, we only support `us`.                                                                                                         |
| apiKey | Required. The [API key](https://www.stedi.com/app/settings/api-keys) used to authenticate your requests. You can create a client without specifying `apiKey`, but if you do, calls to [`send()`](#send) will fail. |

#### Return value

An instance of the `FunctionsClient` class.

#### Exceptions

|       |                      |
| ----- | -------------------- |
| Error | `region` is missing. |

#### Example

```javascript
import { FunctionsClient } from "@stedi/sdk-client-functions";

const functionsClient = new FunctionsClient({
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
import { FunctionsClient } from "@stedi/sdk-client-functions";

const functionsClient = new FunctionsClient({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});

functionsClient.destroy();
```

### send

Sends a command to Stedi Functions.

#### Parameters

```javascript
{
  command: Command;
}
```

`command` must be an instance of one of the [valid Functions commands](#commands).

#### Return value

Depends on the command you sent. Check the description of the command you’re interested in.

#### Exceptions

Depends on the command you sent. Check the description of the command you’re interested in.

#### Example

```javascript
import { FunctionsClient, ReadFunctionCommand } from "@stedi/sdk-client-functions";

const functionName = "YOUR FUNCTION NAME HERE";

async function main() {
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const readFunctionCommand = new ReadFunctionCommand({
      functionName: functionName,
    });
    const readFunctionResult = await functionsClient.send(readFunctionCommand);
    console.info(`The function was created at ${readFunctionResult.createdAt}.`);
  } catch (error) {
    console.info("The function doesn’t exist.");
  }
}

main();
```
