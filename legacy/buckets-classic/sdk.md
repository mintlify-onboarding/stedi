---
title: Buckets SDK
slug: legacy/buckets-classic/sdk
product: ""
description: ""
searchable: true
childPageOrder: []
---

You can access Stedi Buckets programmatically by creating a [`BucketsClient`](#bucketsclient)-object and using it to send [commands](#commands).

The SDK is available for JavaScript and works with TypeScript.

## Installation

To install the Stedi Buckets SDK, run the following command from your project directory.

```sh
npm install @stedi/sdk-client-buckets
```

## Commands

|                                             |                                                                                               |
| ------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [CreateBucketCommand](#createbucketcommand) | Creates a new bucket.                                                                         |
| [DeleteBucketCommand](#deletebucketcommand) | Deletes a bucket.                                                                             |
| [DeleteObjectCommand](#deleteobjectcommand) | Deletes an object from a bucket.                                                              |
| [GetObjectCommand](#getobjectcommand)       | Gets information about the specified object, including a stream for downloading its contents. |
| [ListBucketsCommand](#listbucketscommand)   | Lists all buckets.                                                                            |
| [ListObjectsCommand](#listobjectscommand)   | Lists all objects in the specified bucket.                                                    |
| [PutObjectCommand](#putobjectcommand)       | Uploads an object to the specified bucket using the specified object key.                     |
| [ReadBucketCommand](#readbucketcommand)     | Retrieves metadata of the specified bucket.                                                   |
| [UpdateBucketCommand](#updatebucketcommand) | Updates the bucket configuration.                                                             |

### CreateBucketCommand

Creates a new bucket.

#### Parameters

```javascript
{
  bucketName: string,
  notifications: {
    functions: [{
      functionName: string
    }]
  }
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|                                      |                                                                                                                                                                                                            |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName                           | Required. The name of the bucket you want to create. Bucket names must be globally unique. It may only contain lower case letters, numbers, and dashes. It must have at least 3 characters and at most 63. |
| notifications                        | Optional. Configuration for the notifications the bucket will send to other Stedi products.                                                                                                                |
| notifications.functions.functionName | Required. The name of the function that will receive a notification each time an object is put into the bucket.                                                                                            |

#### Return value

```javascript
{
  $metadata: object,
  bucketName: string,
  createdAt: string,
  notifications: {
    functions: [{
      functionName: string
    }]
  }
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|                                      |                                                                                                                                                             |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata                            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |
| bucketName                           | The name of the created bucket.                                                                                                                             |
| createdAt                            | The date and time at which the bucket was created, e.g. `2022-07-25T08:48:59.523Z`.                                                                         |
| notifications                        | Configuration for the notifications the bucket will send to other Stedi products.                                                                           |
| notifications.functions.functionName | The name of the function that will receive a notification each time an object is put into the bucket.                                                       |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                 |
| ------------------------- | ----------------------------------------------------------------------------------------------- |
| ResourceConflictException | A bucket with the name you specified already exists. Bucket names must be globally unique.      |
| ValidationException       | Either the bucket name you specified is invalid, or the function name you specified is invalid. |

|

#### Example

```javascript
import {
  BucketsClient,
  CreateBucketCommand,
  ValidationException,
  ResourceConflictException,
} from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";
const functionName = "YOUR FUNCTION NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const createBucketCommand = new CreateBucketCommand({
      bucketName: bucketName,
      notifications: {
        functions: [
          {
            functionName: functionName,
          },
        ],
      },
    });
    const createBucketResult = await bucketsClient.send(createBucketCommand);
    console.info(`Created a bucket named ${createBucketResult.bucketName}.`);
  } catch (error) {
    if (error instanceof ValidationException) {
      for (let field of error.fieldList) {
        if (field.path.includes("bucketName")) {
          console.error(`${bucketName} is not a valid name for a bucket.`);
        } else if (field.path.includes("functionName")) {
          console.error(`${functionName} is not a valid name for a function.`);
        } else {
          console.error(`The value you provided for ${field.path} isn’t valid.`);
        }
      }
    } else if (error instanceof ResourceConflictException) {
      console.error(`A bucket with the name ${bucketName} already exists.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### DeleteBucketCommand

Deletes a bucket. The bucket must be empty.

#### Parameters

```javascript
{
  bucketName: string;
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|            |                                                      |
| ---------- | ---------------------------------------------------- |
| bucketName | Required. The name of the bucket you want to delete. |

#### Return value

```javascript
{
  $metadata: object;
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                          |                                                                                                                      |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| InternalFailureException | A bucket with the name you specified doesn’t exist in your account.                                                  |
| ValidationException      | The bucket name you specified isn’t valid. The bucket name may only contain lower case letters, numbers, and dashes. |
| BadRequestException      | The bucket isn’t empty.                                                                                              |

#### Example

```javascript
import {
  BucketsClient,
  DeleteBucketCommand,
  ResourceNotFoundException,
  ValidationException,
} from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const deleteBucketCommand = new DeleteBucketCommand({
      bucketName: bucketName,
    });
    await bucketsClient.send(deleteBucketCommand);
    console.info(`Deleted the bucket with the name ${bucketName}.`);
  } catch (error) {
    if (error instanceof ResourceNotFoundException) {
      console.error(`${bucketName} is not a bucket in your account.`);
    } else if (error instanceof ValidationException) {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.message === "bucket not empty") {
      console.error(`${bucketName} is not empty.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### DeleteObjectCommand

Deletes an object from a bucket.

Specifying a key that doesn’t exist is not considered an error, but specifying a bucket that doesn’t exist is.

#### Parameters

```javascript
{
  bucketName: string,
  key: string
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|            |                                                                                                                                                                                                                                                                                                                                                                                                |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName | Required. The name of the bucket that contains the object you want to delete.                                                                                                                                                                                                                                                                                                                  |
| key        | Required. The key of the object you want to delete. When you delete an object with a key you got through a bucket notification, you should decode the key first. The notification contains a URL encoded key, but this command expects the key not to be URL encoded. The [tutorial on bucket notifications](/docs/buckets/notifications) contains more information, as well as a code sample. |

#### Return value

```javascript
{
  $metadata: object;
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                          |                                                                                                                      |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| InvalidBucketName        | The bucket name you specified isn’t valid. The bucket name may only contain lower case letters, numbers, and dashes. |
| InternalFailureException | A bucket with the name you specified doesn’t exist in your account.                                                  |

#### Example

```javascript
import { BucketsClient, DeleteObjectCommand } from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";
const key = "YOUR OBJECT KEY HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const deleteObjectCommand = new DeleteObjectCommand({
      bucketName: bucketName,
      key: key,
    });
    await bucketsClient.send(deleteObjectCommand);
    console.info(`Object ${key} deleted from bucket ${bucketName}.`);
  } catch (error) {
    if (error.name === "InvalidBucketName") {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.name === "AccessDenied") {
      console.error(`You don’t have access to bucket ${bucketName}.`);
    } else if (error.name === "NoSuchBucket") {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### GetObjectCommand

Gets information about the specified object, including a stream for downloading its contents.

#### Parameters

```javascript
{
  bucketName: string,
  key: string
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|            |                                                                                                                                                                                                                                                                                                                                                                                                    |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName | Required. The name of the bucket you want to retrieve the object from.                                                                                                                                                                                                                                                                                                                             |
| key        | Required. The key of the object you want to retrieve. When you retrieve an object with a key you got through a bucket notification, you should decode the key first. The notification contains a URL encoded key, but this command expects the key not to be URL encoded. The [tutorial on bucket notifications](/docs/buckets/notifications) contains more information, as well as a code sample. |

[TODO]: # "Change the link to the tutorial to point to Stedi.com instead of GitHub, once the tutorial is available there."

#### Return value

```javascript
{
  $metadata: object,
  body: ReadableStream
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |
| body      | A [ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) that provides access to the contents of the object.                     |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                   |                                                                                                                      |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| InvalidBucketName | The bucket name you specified isn’t valid. The bucket name may only contain lower case letters, numbers, and dashes. |
| NoSuchBucket      | A bucket with the name you specified doesn’t exist in your account.                                                  |
| NoSuchKey         | An object with the key you specified doesn’t exist in the bucket.                                                    |

#### Example

```javascript
import { BucketsClient, GetObjectCommand } from "@stedi/sdk-client-buckets";
import consumers from "stream/consumers";

const bucketName = "YOUR BUCKET NAME HERE";
const key = "YOUR OBJECT KEY HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const getObjectCommand = new GetObjectCommand({
      bucketName: bucketName,
      key: key,
    });
    const getObjectResult = await bucketsClient.send(getObjectCommand);
    const contents = await consumers.text(getObjectResult.body);
    console.info(contents);
  } catch (error) {
    if (error.name === "InvalidBucketName") {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.name === "AccessDenied") {
      console.error(`You don’t have access to bucket ${bucketName}.`);
    } else if (error.name === "NoSuchBucket") {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else if (error.name === "NoSuchKey") {
      console.error(`Object ${key} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### ListBucketsCommand

Retrieve a list of all buckets.

#### Parameters

```javascript
{
  pageSize: number?,
  pageToken: string?
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |                                                                                                                                                                                                  |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| pageSize  | Optional. The maximum number of buckets you want to retrieve. Defaults to 25 and can’t be higher than 50.                                                                                        |
| pageToken | Optional. A token that allows you to retrieve the next page of results. It should have the same value as the `nextPageToken` field you received the last time you sent the `ListBucketsCommand`. |

#### Return value

```javascript
{
  $metadata: object,
  items: [{
    bucketName: string,
    createdAt: string
  }],
  nextPageToken: string?
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|                  |                                                                                                                                                                                                                                       |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata        | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                           |
| items            | An array of buckets.                                                                                                                                                                                                                  |
| items.bucketName | The name of the bucket.                                                                                                                                                                                                               |
| items.createdAt  | The date and time at which the bucket was created, e.g. `2022-07-25T08:48:59.523Z`.                                                                                                                                                   |
| nextPageToken    | A token that allows you to retrieve the next page of result. The next time you call `listBuckets()`, set `pageToken` to this value to retrieve additional results. If there are no more results, `nextPageToken` will be `undefined`. |

#### Exceptions

None.

#### Example

```javascript
import { BucketsClient, ListBucketsCommand } from "@stedi/sdk-client-buckets";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    let pageToken = undefined;
    do {
      const listBucketsCommand = new ListBucketsCommand({
        pageSize: 7,
        pageToken: pageToken,
      });
      const listBucketsResult = await bucketsClient.send(listBucketsCommand);
      console.info(listBucketsResult.items);

      pageToken = listBucketsResult.nextPageToken;
    } while (pageToken !== undefined);
  } catch (error) {
    console.error(error);
  }
}

main();
```

### ListObjectsCommand

Retrieve a list of all objects in the specified bucket.

#### Parameters

```javascript
{
  bucketName: string,
  pageSize: number?,
  pageToken: string?
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|            |                                                                                                                                                                                           |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName | Required. The name of the bucket whose objects you want to list.                                                                                                                          |
| pageSize   | Optional. The maximum number of buckets you want to receive. Defaults to 25 and can’t be higher than 50.                                                                                  |
| pageToken  | Optional. A token that allows you to retrieve the next page of results. It should have the same value as the `nextPageToken` field you received the last time you called `listObjects()`. |

#### Return value

```javascript
{
  $metadata: object,
  items: [{
    key: string,
    size: number
  }],
  nextPageToken: string?
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|               |                                                                                                                                                                                                                                       |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata     | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them.                                                                           |
| items         | An array of objects.                                                                                                                                                                                                                  |
| items.key     | The key of the object.                                                                                                                                                                                                                |
| items.size    | The size of the object in bytes.                                                                                                                                                                                                      |
| nextPageToken | A token that allows you to retrieve the next page of result. The next time you call `listObjects()`, set `pageToken` to this value to retrieve additional results. If there are no more results, `nextPageToken` will be `undefined`. |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                          |                                                                                                                      |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| ValidationException      | The bucket name you specified isn’t valid. The bucket name may only contain lower case letters, numbers, and dashes. |
| InternalFailureException | A bucket with the name you specified doesn’t exist in your account.                                                  |

#### Example

```javascript
import { BucketsClient, ListObjectsCommand } from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    let pageToken = undefined;
    do {
      const listObjectsCommand = new ListObjectsCommand({
        bucketName: bucketName,
        pageSize: 7,
        pageToken: pageToken,
      });

      const listObjectsResult = await bucketsClient.send(listObjectsCommand);
      console.info(listObjectsResult.items);

      pageToken = listObjectsResult.nextPageToken;
    } while (pageToken !== undefined);
  } catch (error) {
    if (error.name === "InvalidBucketName") {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.name === "AccessDenied") {
      console.error(`${bucketName} is not a bucket in your account.`);
    } else if (error.name === "NoSuchBucket") {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### PutObjectCommand

Uploads an object to the specified bucket using the specified object key.

If you specify a key that doesn’t exist yet, an object with that key will be created. If the key already exists, the object will be replaced.

#### Parameters

```javascript
{
  bucketName: string,
  key: string,
  body: ReadableStream | string
}
```

The constructor takes an object as its only argument. All parameters are fields within this object.

|            |                                                                                                                                                                                                                                                                                                                                                                                                |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName | Required. The name of the bucket you want to upload an object to.                                                                                                                                                                                                                                                                                                                              |
| key        | Required. The key of the object you want to upload. When you upload an object with a key you got through a bucket notification, you should decode the key first. The notification contains a URL encoded key, but this command expects the key not to be URL encoded. The [tutorial on bucket notifications](/docs/buckets/notifications) contains more information, as well as a code sample. |
| body       | Required. A [ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) that provides access to the contents you want to upload, or a string with the contents.                                                                                                                                                                                                          |

[TODO]: # "Change the link to the tutorial to point to Stedi.com instead of GitHub, once the tutorial is available there."

#### Return value

```javascript
{
  $metadata: object;
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|           |                                                                                                                                                             |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                   |                                                                                        |
| ----------------- | -------------------------------------------------------------------------------------- | ----------------------------- |
| InvalidBucketName | The bucket name you specified isn’t valid. The bucket name may only contain lower case | letters, numbers, and dashes. |
| NoSuchBucket      | A bucket with the name you specified doesn’t exist in your account.                    |

#### Example

Store a string.

```javascript
import { BucketsClient, PutObjectCommand } from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";
const key = "YOUR OBJECT KEY HERE";
const contents = "YOUR OBJECT CONTENTS HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const putObjectCommand = new PutObjectCommand({
      bucketName: bucketName,
      key: key,
      body: contents,
    });
    await bucketsClient.send(putObjectCommand);
  } catch (error) {
    if (error.name === "InvalidBucketName") {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.name === "AccessDenied") {
      console.error(`${bucketName} is not a bucket in your account.`);
    } else if (error.name === "NoSuchBucket") {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

Store the contents of a local file.

```javascript
import { BucketsClient, PutObjectCommand } from "@stedi/sdk-client-buckets";
import { createReadStream } from "fs";

const bucketName = "YOUR BUCKET NAME HERE";
const key = "YOUR OBJECT KEY HERE";
const fileName = "YOUR LOCAL FILE NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const putObjectCommand = new PutObjectCommand({
      bucketName: bucketName,
      key: key,
      body: createReadStream(fileName),
    });
    await bucketsClient.send(putObjectCommand);
  } catch (error) {
    if (error.name === "InvalidBucketName") {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error.name === "AccessDenied") {
      console.error(`${bucketName} is not a bucket in your account.`);
    } else if (error.name === "NoSuchBucket") {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### ReadBucketCommand

Retrieves the metadata of the specified bucket.

#### Parameters

```javascript
{
  bucketName: string;
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|            |                                                        |
| ---------- | ------------------------------------------------------ |
| bucketName | Required. The name of the bucket you want to retrieve. |

#### Return value

```javascript
{
  $metadata: object,
  bucketName: string,
  createdAt: string,
  notifications: {
    functions: [{
      functionName: string
    }]
  }
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|                                      |                                                                                                                                                             |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata                            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |
| bucketName                           | The name of the bucket.                                                                                                                                     |
| createdAt                            | The date and time at which the bucket was created, e.g. `2022-07-25T08:48:59.523Z`.                                                                         |
| notifications                        | Configuration for the notifications the bucket will send to other Stedi products.                                                                           |
| notifications.functions.functionName | The name of the function that will receive a notification each time an object is put into the bucket.                                                       |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                           |                                                                                                                      |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| ValidationException       | The bucket name you specified isn’t valid. The bucket name may only contain lower case letters, numbers, and dashes. |
| ResourceNotFoundException | A bucket with the name you specified doesn’t exist in your account.                                                  |

#### Example

```javascript
import {
  BucketsClient,
  ReadBucketCommand,
  ValidationException,
  ResourceNotFoundException,
} from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const readBucketCommand = new ReadBucketCommand({
      bucketName: bucketName,
    });
    const readBucketResult = await bucketsClient.send(readBucketCommand);
    console.info(`Bucket ${readBucketResult.bucketName} was created at ${readBucketResult.createdAt}.`);
  } catch (error) {
    if (error instanceof ValidationException) {
      console.error(`${bucketName} is not a valid name for a bucket.`);
    } else if (error instanceof ResourceNotFoundException) {
      console.error(`Bucket ${bucketName} doesn’t exist.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

### UpdateBucketCommand

Updates the configuration for [bucket notifications](/docs/buckets/notifications).

#### Parameters

```javascript
{
  bucketName: string,
  notifications: {
    functions: [{
      functionName: string
    }]
  }
}
```

The constructor of the command takes an object as its only arguments. All parameters are fields within this object.

|                                      |                                                                                                                                                                                                                                                       |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| bucketName                           | Required. The name of the bucket you want to update. You can’t change the bucket’s name.                                                                                                                                                              |
| notifications                        | Optional. Configuration for the notifications the bucket will send to other Stedi products. If the bucket had notifications configured before, they will be replaced. If you want to keep the previous notifications, you have to include them again. |
| notifications.functions.functionName | Required. The name of the function that will receive a notification each time an object is put into the bucket.                                                                                                                                       |

#### Return value

```javascript
{
  $metadata: object,
  bucketName: string,
  createdAt: string,
  notifications: {
    functions: [{
      functionName: string
    }]
  }
}
```

The [`send()`](#send) method of the [Buckets client](#bucketsclient) will return the result of the command.

|                                      |                                                                                                                                                             |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $metadata                            | An object containing data about the request the client made to the backend. The fields in this object are subject to change and you shouldn’t rely on them. |
| bucketName                           | The name of the created bucket.                                                                                                                             |
| createdAt                            | The date and time at which the bucket was created, e.g. `2022-07-25T08:48:59.523Z`.                                                                         |
| notifications                        | Configuration for the notifications the bucket will send to other Stedi products.                                                                           |
| notifications.functions.functionName | The name of the function that will receive a notification each time an object is put into the bucket.                                                       |

#### Exceptions

The [`send()`](#send) method of the [Buckets client](#bucketsclient) may raise one of the following exceptions when executing the command.

|                     |                                                                                                 |
| ------------------- | ----------------------------------------------------------------------------------------------- |
| BadRequestException | Either the bucket you specified doesn’t exist, or the function you specified doesn’t exist.     |
| ValidationException | Either the bucket name you specified is invalid, or the function name you specified is invalid. |

#### Example

```javascript
import { BucketsClient, UpdateBucketCommand, ValidationException } from "@stedi/sdk-client-buckets";

const bucketName = "demo-trigger-bucket-joost";
const functionName = "test-function";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const updateBucketCommand = new UpdateBucketCommand({
      bucketName: bucketName,
      notifications: {
        functions: [
          {
            functionName: functionName,
          },
        ],
      },
    });
    const updateBucketResult = await bucketsClient.send(updateBucketCommand);
    console.info(`Updated a bucket named ${updateBucketResult.bucketName}.`);
  } catch (error) {
    if (error instanceof ValidationException) {
      for (let field of error.fieldList) {
        if (field.path.includes("bucketName")) {
          console.error(`${bucketName} is not a valid name for a bucket.`);
        } else if (field.path.includes("functionName")) {
          console.error(`${functionName} is not a valid name for a function.`);
        } else {
          console.error(`The value you provided for ${field.path} isn’t valid.`);
        }
      }
    } else if (error.message === "Resource Stedi::Cloud::Bucket property bucketName must not change") {
      console.error(`A bucket with the name ${bucketName} doesn’t exists.`);
    } else if (error.message === "Unable to validate the following destination configurations") {
      console.error(`A function with the name ${functionName} doesn’t exists.`);
    } else {
      console.error(error);
    }
  }
}

main();
```

## BucketsClient

|                             |                                                                     |
| --------------------------- | ------------------------------------------------------------------- |
| [constructor](#constructor) | Creates a client that allows you to send commands to Stedi Buckets. |
| [destroy()](#destroy)       | Shuts down the client and its underlying resources.                 |
| [send()](#send)             | Sends a command.                                                    |

### constructor

Creates a client that allows you to send commands to Stedi Buckets.

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
| region | Required. The region that hosts the buckets you want to connect to. At the moment, we only support `us`.                                                                                                                              |
| apiKey | Required. The [API key](https://www.stedi.com/docs/accounts-and-billing/authentication) used to authenticate your requests. You can create a client without specifying `apiKey`, but if you do, calls to [`send()`](#send) will fail. |

#### Return value

An instance of the `BucketsClient` class.

#### Exceptions

|       |                      |
| ----- | -------------------- |
| Error | `region` is missing. |

#### Example

```javascript
import { BucketsClient } from "@stedi/sdk-client-buckets";

const bucketsClient = new BucketsClient({
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
import { BucketsClient } from "@stedi/sdk-client-buckets";

const bucketsClient = new BucketsClient({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});

bucketsClient.destroy();
```

### send

Sends a command to Stedi Buckets.

#### Parameters

```javascript
{
  command: Command;
}
```

`command` must be an instance of one of the [valid Buckets commands](#commands).

#### Return value

Depends on the command you sent. Check the description of the command you’re interested in.

#### Exceptions

Depends on the command you sent. Check the description of the command you’re interested in.

#### Example

```javascript
import { BucketsClient, ReadBucketCommand } from "@stedi/sdk-client-buckets";

const bucketName = "YOUR BUCKET NAME HERE";

async function main() {
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  try {
    const readBucketCommand = new ReadBucketCommand({
      bucketName: bucketName,
    });
    const readBucketResult = await bucketsClient.send(readBucketCommand);
    console.info(`The bucket was created at ${readBucketResult.createdAt}.`);
  } catch (error) {
    console.info("The bucket doesn’t exist.");
  }
}

main();
```
