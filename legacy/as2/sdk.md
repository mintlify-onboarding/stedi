---
title: AS2 SDK
sidebarTitle: SDK reference
slug: legacy/as2/sdk
lifecycle: legacy
product: as2
description: With the Stedi AS2 SDK you can programmatically access Stedi AS2.
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring AS2 connections from within [Stedi Core](/docs/core/configuration/connections). Legacy AS2 is not enabled in your account by default. Please [request access](/app/as2) and we will enable it for you.

You can access Stedi AS2 programmatically by creating an [`As2Client`](#as2client) and using it to send [commands](#commands).

The SDK is available for JavaScript and works with TypeScript.

## Installation

To install the Stedi AS2 SDK, run the following command from your project directory.

```sh
npm install @stedi/sdk-client-as2
```

## Commands

|                                                           |                                      |
| --------------------------------------------------------- | ------------------------------------ |
| [CreateAgreementCommand](#createagreementcommand)         | Create an AS2 agreement.             |
| [CreateConnectorCommand](#createconnectorcommand)         | Create an AS2 connector.             |
| [CreateProfileCommand](#createprofilecommand)             | Create an AS2 profile.               |
| [CreateServerCommand](#createservercommand)               | Create an AS2 server.                |
| [DeleteAgreementCommand](#deleteagreementcommand)         | Delete an existing AS2 agreement.    |
| [DeleteCertificateCommand](#deletecertificatecommand)     | Delete an existing AS2 certificate.  |
| [DeleteConnectorCommand](#deleteconnectorcommand)         | Delete an existing AS2 connector.    |
| [DeleteProfileCommand](#deleteprofilecommand)             | Delete an existing AS2 profile.      |
| [DeleteServerCommand](#deleteservercommand)               | Delete an existing AS2 server.       |
| [DescribeAgreementCommand](#describeagreementcommand)     | Read an AS2 agreement.               |
| [DescribeCertificateCommand](#describecertificatecommand) | Retrieve an existing AS2 server.     |
| [DescribeConnectorCommand](#describeconnectorcommand)     | Retrieve an existing AS2 connector.  |
| [DescribeProfileCommand](#describeprofilecommand)         | Retrieve an existing AS2 profile.    |
| [DescribeServerCommand](#describeservercommand)           | Retrieve an existing AS2 server.     |
| [ImportCertificateCommand](#importcertificatecommand)     | Import an AS2 certificate.           |
| [ListAgreementsCommand](#listagreementscommand)           | Retrieve a list of AS2 agreements.   |
| [ListCertificatesCommand](#listcertificatescommand)       | Retrieve a list of AS2 certificates. |
| [ListConnectorsCommand](#listconnectorscommand)           | Retrieve a list of AS2 connectors.   |
| [ListProfilesCommand](#listprofilescommand)               | Retrieve a list of AS2 profiles.     |
| [ListServersCommand](#listserverscommand)                 | Retrieve a list of AS2 servers.      |
| [StartFileTransferCommand](#startfiletransfercommand)     | Send as2 file using a connector.     |
| [UpdateAgreementCommand](#updateagreementcommand)         | Update an existing AS2 Profile.      |
| [UpdateCertificateCommand](#updatecertificatecommand)     | Update an existing AS2 certificate.  |
| [UpdateConnectorCommand](#updateconnectorcommand)         | Update an existing AS2 connector.    |
| [UpdateProfileCommand](#updateprofilecommand)             | Update an existing AS2 Profile.      |

### CreateAgreementCommand

Create an AS2 agreement.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  serverId: string,
  localProfileId: string,
  partnerProfileId: string,
  baseDirectory: string,
  status: enum,
  description: string,
  agreementName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                  |                                                 |
| ---------------- | ----------------------------------------------- |
| agreementName    | Required.                                       |
| baseDirectory    | Required.                                       |
| description      | Required.                                       |
| idempotencyKey   | Optional.                                       |
| localProfileId   | Required.                                       |
| partnerProfileId | Required.                                       |
| serverId         | Required.                                       |
| status           | Required. Allowed values: `ACTIVE`, `INACTIVE`. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  agreementName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| agreementName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  CreateAgreementCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const createAgreementCommand = new CreateAgreementCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    serverId: "<YOUR_SERVER_ID_HERE>",
    localProfileId: "<YOUR_LOCAL_PROFILE_ID_HERE>",
    partnerProfileId: "<YOUR_PARTNER_PROFILE_ID_HERE>",
    baseDirectory: "<YOUR_BASE_DIRECTORY_HERE>",
    status: <YOUR_STATUS_HERE>,
    description: "<YOUR_DESCRIPTION_HERE>",
    agreementName: "<YOUR_AGREEMENT_NAME_HERE>"
  });
  const createAgreementResult = await as2Client.send(createAgreementCommand);
  console.info(createAgreementResult);
}

main();
```

### CreateConnectorCommand

Create an AS2 connector.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  localProfileId: string,
  partnerProfileId: string,
  url: string,
  messageSubject?: string,
  compression?: enum,
  encryptionAlgorithm?: enum,
  signingAlgorithm?: enum,
  mdnSigningAlgorithm?: enum,
  mdnResponse?: enum,
  connectorName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                     |                                                                                    |
| ------------------- | ---------------------------------------------------------------------------------- |
| compression         | Optional. Allowed values: `ZLIB`, `DISABLED`.                                      |
| connectorName       | Required.                                                                          |
| encryptionAlgorithm | Optional. Allowed values: `AES128_CBC`, `AES192_CBC`, `AES256_CBC`, `NONE`.        |
| idempotencyKey      | Optional.                                                                          |
| localProfileId      | Required.                                                                          |
| mdnResponse         | Optional. Allowed values: `SYNC`, `NONE`.                                          |
| mdnSigningAlgorithm | Optional. Allowed values: `SHA256`, `SHA384`, `SHA512`, `SHA1`, `NONE`, `DEFAULT`. |
| messageSubject      | Optional.                                                                          |
| partnerProfileId    | Required.                                                                          |
| signingAlgorithm    | Optional. Allowed values: `SHA256`, `SHA384`, `SHA512`, `SHA1`, `NONE`.            |
| url                 | Required.                                                                          |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  connectorName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| connectorName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  CreateConnectorCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const createConnectorCommand = new CreateConnectorCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    localProfileId: "<YOUR_LOCAL_PROFILE_ID_HERE>",
    partnerProfileId: "<YOUR_PARTNER_PROFILE_ID_HERE>",
    url: "<YOUR_URL_HERE>",
    messageSubject: "<YOUR_MESSAGE_SUBJECT_HERE>",
    compression: <YOUR_COMPRESSION_HERE>,
    encryptionAlgorithm: <YOUR_ENCRYPTION_ALGORITHM_HERE>,
    signingAlgorithm: <YOUR_SIGNING_ALGORITHM_HERE>,
    mdnSigningAlgorithm: <YOUR_MDN_SIGNING_ALGORITHM_HERE>,
    mdnResponse: <YOUR_MDN_RESPONSE_HERE>,
    connectorName: "<YOUR_CONNECTOR_NAME_HERE>"
  });
  const createConnectorResult = await as2Client.send(createConnectorCommand);
  console.info(createConnectorResult);
}

main();
```

### CreateProfileCommand

Create an AS2 profile.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  as2Id: string,
  profileType: enum,
  certificateIds: [string],
  profileName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |                                               |
| -------------- | --------------------------------------------- |
| as2Id          | Required.                                     |
| certificateIds | Optional.                                     |
| idempotencyKey | Optional.                                     |
| profileName    | Required.                                     |
| profileType    | Required. Allowed values: `LOCAL`, `PARTNER`. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  profileName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| profileName                   |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  CreateProfileCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const createProfileCommand = new CreateProfileCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    as2Id: "<YOUR_AS2_ID_HERE>",
    profileType: <YOUR_PROFILE_TYPE_HERE>,
    certificateIds: [
      <YOUR_CERTIFICATE_IDS>
    ],
    profileName: "<YOUR_PROFILE_NAME_HERE>"
  });
  const createProfileResult = await as2Client.send(createProfileCommand);
  console.info(createProfileResult);
}

main();
```

### CreateServerCommand

Create an AS2 server.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  serverName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| idempotencyKey | Optional. |
| serverName     | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  serverName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |
| serverName                    |                                                                  |

#### Example

```javascript
import { As2Client, CreateServerCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const createServerCommand = new CreateServerCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    serverName: "<YOUR_SERVER_NAME_HERE>",
  });
  const createServerResult = await as2Client.send(createServerCommand);
  console.info(createServerResult);
}

main();
```

### DeleteAgreementCommand

Delete an existing AS2 agreement.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  agreementName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| agreementName  | Required. |
| idempotencyKey | Optional. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  agreementName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| agreementName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DeleteAgreementCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteAgreementCommand = new DeleteAgreementCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    agreementName: "<YOUR_AGREEMENT_NAME_HERE>",
  });
  const deleteAgreementResult = await as2Client.send(deleteAgreementCommand);
  console.info(deleteAgreementResult);
}

main();
```

### DeleteCertificateCommand

Delete an existing AS2 certificate.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  certificateName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                 |           |
| --------------- | --------- |
| certificateName | Required. |
| idempotencyKey  | Optional. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  certificateName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| certificateName               |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DeleteCertificateCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteCertificateCommand = new DeleteCertificateCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    certificateName: "<YOUR_CERTIFICATE_NAME_HERE>",
  });
  const deleteCertificateResult = await as2Client.send(deleteCertificateCommand);
  console.info(deleteCertificateResult);
}

main();
```

### DeleteConnectorCommand

Delete an existing AS2 connector.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  connectorName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| connectorName  | Required. |
| idempotencyKey | Optional. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  connectorName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| connectorName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DeleteConnectorCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteConnectorCommand = new DeleteConnectorCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    connectorName: "<YOUR_CONNECTOR_NAME_HERE>",
  });
  const deleteConnectorResult = await as2Client.send(deleteConnectorCommand);
  console.info(deleteConnectorResult);
}

main();
```

### DeleteProfileCommand

Delete an existing AS2 profile.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  profileName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| idempotencyKey | Optional. |
| profileName    | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  profileName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| profileName                   |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DeleteProfileCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteProfileCommand = new DeleteProfileCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    profileName: "<YOUR_PROFILE_NAME_HERE>",
  });
  const deleteProfileResult = await as2Client.send(deleteProfileCommand);
  console.info(deleteProfileResult);
}

main();
```

### DeleteServerCommand

Delete an existing AS2 server.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  serverName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| idempotencyKey | Optional. |
| serverName     | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  serverName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |
| serverName                    |                                                                  |

#### Example

```javascript
import { As2Client, DeleteServerCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const deleteServerCommand = new DeleteServerCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    serverName: "<YOUR_SERVER_NAME_HERE>",
  });
  const deleteServerResult = await as2Client.send(deleteServerCommand);
  console.info(deleteServerResult);
}

main();
```

### DescribeAgreementCommand

Read an AS2 agreement.

#### Parameters

```javascript
{
  agreementName: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|               |           |
| ------------- | --------- |
| agreementName | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  }
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DescribeAgreementCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const describeAgreementCommand = new DescribeAgreementCommand({
    agreementName: "<YOUR_AGREEMENT_NAME_HERE>",
  });
  const describeAgreementResult = await as2Client.send(describeAgreementCommand);
  console.info(describeAgreementResult);
}

main();
```

### DescribeCertificateCommand

Retrieve an existing AS2 server.

#### Parameters

```javascript
{
  certificateName: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                 |           |
| --------------- | --------- |
| certificateName | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  }
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DescribeCertificateCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const describeCertificateCommand = new DescribeCertificateCommand({
    certificateName: "<YOUR_CERTIFICATE_NAME_HERE>",
  });
  const describeCertificateResult = await as2Client.send(describeCertificateCommand);
  console.info(describeCertificateResult);
}

main();
```

### DescribeConnectorCommand

Retrieve an existing AS2 connector.

#### Parameters

```javascript
{
  connectorName: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|               |           |
| ------------- | --------- |
| connectorName | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  }
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DescribeConnectorCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const describeConnectorCommand = new DescribeConnectorCommand({
    connectorName: "<YOUR_CONNECTOR_NAME_HERE>",
  });
  const describeConnectorResult = await as2Client.send(describeConnectorCommand);
  console.info(describeConnectorResult);
}

main();
```

### DescribeProfileCommand

Retrieve an existing AS2 profile.

#### Parameters

```javascript
{
  profileName: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|             |           |
| ----------- | --------- |
| profileName | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  }
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, DescribeProfileCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const describeProfileCommand = new DescribeProfileCommand({
    profileName: "<YOUR_PROFILE_NAME_HERE>",
  });
  const describeProfileResult = await as2Client.send(describeProfileCommand);
  console.info(describeProfileResult);
}

main();
```

### DescribeServerCommand

Retrieve an existing AS2 server.

#### Parameters

```javascript
{
  serverName: string;
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|            |           |
| ---------- | --------- |
| serverName | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  serverUrl: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |
| serverUrl                     |                                                                  |

#### Example

```javascript
import { As2Client, DescribeServerCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const describeServerCommand = new DescribeServerCommand({
    serverName: "<YOUR_SERVER_NAME_HERE>",
  });
  const describeServerResult = await as2Client.send(describeServerCommand);
  console.info(describeServerResult);
}

main();
```

### ImportCertificateCommand

Import an AS2 certificate.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  certificate: string,
  certificateChain?: string,
  privateKey?: string,
  activeDate?: string,
  inactiveDate?: string,
  description: string,
  usage: string,
  certificateName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                  |           |
| ---------------- | --------- |
| activeDate       | Optional. |
| certificate      | Required. |
| certificateChain | Optional. |
| certificateName  | Required. |
| description      | Required. |
| idempotencyKey   | Optional. |
| inactiveDate     | Optional. |
| privateKey       | Optional. |
| usage            | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  certificateName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| certificateName               |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, ImportCertificateCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const importCertificateCommand = new ImportCertificateCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    certificate: "<YOUR_CERTIFICATE_HERE>",
    certificateChain: "<YOUR_CERTIFICATE_CHAIN_HERE>",
    privateKey: "<YOUR_PRIVATE_KEY_HERE>",
    activeDate: "<YOUR_ACTIVE_DATE_HERE>",
    inactiveDate: "<YOUR_INACTIVE_DATE_HERE>",
    description: "<YOUR_DESCRIPTION_HERE>",
    usage: "<YOUR_USAGE_HERE>",
    certificateName: "<YOUR_CERTIFICATE_NAME_HERE>",
  });
  const importCertificateResult = await as2Client.send(importCertificateCommand);
  console.info(importCertificateResult);
}

main();
```

### ListAgreementsCommand

Retrieve a list of AS2 agreements.

#### Parameters

```javascript
{
  pageSize?: integer,
  pageToken?: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |           |
| --------- | --------- |
| pageSize  | Optional. |
| pageToken | Optional. |

#### Return value

```javascript
{
  items: [{
    resourceDetail: {
      status: enum,
      statusMessage: string,
      idempotencyKey: string,
      createdAt: timestamp,
      updatedAt: timestamp
    },
    agreementName: string
  }],
  nextPageToken: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                                     |                                                                  |
| ----------------------------------- | ---------------------------------------------------------------- |
| items                               |                                                                  |
| items.resourceDetail                |                                                                  |
| items.resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| items.resourceDetail.statusMessage  |                                                                  |
| items.resourceDetail.idempotencyKey |                                                                  |
| items.resourceDetail.createdAt      |                                                                  |
| items.resourceDetail.updatedAt      |                                                                  |
| items.agreementName                 |                                                                  |
| nextPageToken                       |                                                                  |

#### Example

```javascript
import { As2Client, ListAgreementsCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listAgreementsCommand = new ListAgreementsCommand({
      pageSize: 25,
      pageToken: pageToken,
    });

    const listAgreementsResult = await listAgreementsCommand.send(listAgreementsCommand);
    console.info(listAgreementsResult.items);

    pageToken = listAgreementsResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ListCertificatesCommand

Retrieve a list of AS2 certificates.

#### Parameters

```javascript
{
  pageSize?: integer,
  pageToken?: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |           |
| --------- | --------- |
| pageSize  | Optional. |
| pageToken | Optional. |

#### Return value

```javascript
{
  items: [{
    resourceDetail: {
      status: enum,
      statusMessage: string,
      idempotencyKey: string,
      createdAt: timestamp,
      updatedAt: timestamp
    },
    certificateName: string
  }],
  nextPageToken: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                                     |                                                                  |
| ----------------------------------- | ---------------------------------------------------------------- |
| items                               |                                                                  |
| items.resourceDetail                |                                                                  |
| items.resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| items.resourceDetail.statusMessage  |                                                                  |
| items.resourceDetail.idempotencyKey |                                                                  |
| items.resourceDetail.createdAt      |                                                                  |
| items.resourceDetail.updatedAt      |                                                                  |
| items.certificateName               |                                                                  |
| nextPageToken                       |                                                                  |

#### Example

```javascript
import { As2Client, ListCertificatesCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listCertificatesCommand = new ListCertificatesCommand({
      pageSize: 25,
      pageToken: pageToken,
    });

    const listCertificatesResult = await listCertificatesCommand.send(listCertificatesCommand);
    console.info(listCertificatesResult.items);

    pageToken = listCertificatesResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ListConnectorsCommand

Retrieve a list of AS2 connectors.

#### Parameters

```javascript
{
  pageSize?: integer,
  pageToken?: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |           |
| --------- | --------- |
| pageSize  | Optional. |
| pageToken | Optional. |

#### Return value

```javascript
{
  items: [{
    resourceDetail: {
      status: enum,
      statusMessage: string,
      idempotencyKey: string,
      createdAt: timestamp,
      updatedAt: timestamp
    },
    connectorName: string
  }],
  nextPageToken: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                                     |                                                                  |
| ----------------------------------- | ---------------------------------------------------------------- |
| items                               |                                                                  |
| items.resourceDetail                |                                                                  |
| items.resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| items.resourceDetail.statusMessage  |                                                                  |
| items.resourceDetail.idempotencyKey |                                                                  |
| items.resourceDetail.createdAt      |                                                                  |
| items.resourceDetail.updatedAt      |                                                                  |
| items.connectorName                 |                                                                  |
| nextPageToken                       |                                                                  |

#### Example

```javascript
import { As2Client, ListConnectorsCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listConnectorsCommand = new ListConnectorsCommand({
      pageSize: 25,
      pageToken: pageToken,
    });

    const listConnectorsResult = await listConnectorsCommand.send(listConnectorsCommand);
    console.info(listConnectorsResult.items);

    pageToken = listConnectorsResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ListProfilesCommand

Retrieve a list of AS2 profiles.

#### Parameters

```javascript
{
  pageSize?: integer,
  pageToken?: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |           |
| --------- | --------- |
| pageSize  | Optional. |
| pageToken | Optional. |

#### Return value

```javascript
{
  items: [{
    resourceDetail: {
      status: enum,
      statusMessage: string,
      idempotencyKey: string,
      createdAt: timestamp,
      updatedAt: timestamp
    },
    profileName: string
  }],
  nextPageToken: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                                     |                                                                  |
| ----------------------------------- | ---------------------------------------------------------------- |
| items                               |                                                                  |
| items.resourceDetail                |                                                                  |
| items.resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| items.resourceDetail.statusMessage  |                                                                  |
| items.resourceDetail.idempotencyKey |                                                                  |
| items.resourceDetail.createdAt      |                                                                  |
| items.resourceDetail.updatedAt      |                                                                  |
| items.profileName                   |                                                                  |
| nextPageToken                       |                                                                  |

#### Example

```javascript
import { As2Client, ListProfilesCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listProfilesCommand = new ListProfilesCommand({
      pageSize: 25,
      pageToken: pageToken,
    });

    const listProfilesResult = await listProfilesCommand.send(listProfilesCommand);
    console.info(listProfilesResult.items);

    pageToken = listProfilesResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### ListServersCommand

Retrieve a list of AS2 servers.

#### Parameters

```javascript
{
  pageSize?: integer,
  pageToken?: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|           |           |
| --------- | --------- |
| pageSize  | Optional. |
| pageToken | Optional. |

#### Return value

```javascript
{
  items: [{
    resourceDetail: {
      status: enum,
      statusMessage: string,
      idempotencyKey: string,
      createdAt: timestamp,
      updatedAt: timestamp
    },
    serverName: string
  }],
  nextPageToken: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                                     |                                                                  |
| ----------------------------------- | ---------------------------------------------------------------- |
| items                               |                                                                  |
| items.resourceDetail                |                                                                  |
| items.resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| items.resourceDetail.statusMessage  |                                                                  |
| items.resourceDetail.idempotencyKey |                                                                  |
| items.resourceDetail.createdAt      |                                                                  |
| items.resourceDetail.updatedAt      |                                                                  |
| items.serverName                    |                                                                  |
| nextPageToken                       |                                                                  |

#### Example

```javascript
import { As2Client, ListServersCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  let pageToken = undefined;
  do {
    const listServersCommand = new ListServersCommand({
      pageSize: 25,
      pageToken: pageToken,
    });

    const listServersResult = await listServersCommand.send(listServersCommand);
    console.info(listServersResult.items);

    pageToken = listServersResult.nextPageToken;
  } while (pageToken !== undefined);
}

main();
```

### StartFileTransferCommand

Send as2 file using a connector.

#### Parameters

```javascript
{
  connectorId?: string,
  sendFilePaths: [string]
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object. If you don’t wish to pass arguments to the constructor, you must still provide an empty object.

|               |           |
| ------------- | --------- |
| connectorId   | Optional. |
| sendFilePaths | Optional. |

#### Return value

```javascript
{
  transferId: string;
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|            |     |
| ---------- | --- |
| transferId |     |

#### Example

```javascript
import {
  As2Client,
  StartFileTransferCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const startFileTransferCommand = new StartFileTransferCommand({
    connectorId: "<YOUR_CONNECTOR_ID_HERE>",
    sendFilePaths: [
      <YOUR_SEND_FILE_PATHS>
    ]
  });
  const startFileTransferResult = await as2Client.send(startFileTransferCommand);
  console.info(startFileTransferResult);
}

main();
```

### UpdateAgreementCommand

Update an existing AS2 Profile.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  serverId?: string,
  localProfileId?: string,
  partnerProfileId?: string,
  baseDirectory?: string,
  status?: enum,
  description?: string,
  agreementName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                  |                                                 |
| ---------------- | ----------------------------------------------- |
| agreementName    | Required.                                       |
| baseDirectory    | Optional.                                       |
| description      | Optional.                                       |
| idempotencyKey   | Optional.                                       |
| localProfileId   | Optional.                                       |
| partnerProfileId | Optional.                                       |
| serverId         | Optional.                                       |
| status           | Optional. Allowed values: `ACTIVE`, `INACTIVE`. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  agreementName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| agreementName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  UpdateAgreementCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const updateAgreementCommand = new UpdateAgreementCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    serverId: "<YOUR_SERVER_ID_HERE>",
    localProfileId: "<YOUR_LOCAL_PROFILE_ID_HERE>",
    partnerProfileId: "<YOUR_PARTNER_PROFILE_ID_HERE>",
    baseDirectory: "<YOUR_BASE_DIRECTORY_HERE>",
    status: <YOUR_STATUS_HERE>,
    description: "<YOUR_DESCRIPTION_HERE>",
    agreementName: "<YOUR_AGREEMENT_NAME_HERE>"
  });
  const updateAgreementResult = await as2Client.send(updateAgreementCommand);
  console.info(updateAgreementResult);
}

main();
```

### UpdateCertificateCommand

Update an existing AS2 certificate.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  description?: string,
  activeDate?: string,
  inactiveDate?: string,
  certificateName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                 |           |
| --------------- | --------- |
| activeDate      | Optional. |
| certificateName | Required. |
| description     | Optional. |
| idempotencyKey  | Optional. |
| inactiveDate    | Optional. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  certificateName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| certificateName               |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import { As2Client, UpdateCertificateCommand } from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  const updateCertificateCommand = new UpdateCertificateCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    description: "<YOUR_DESCRIPTION_HERE>",
    activeDate: "<YOUR_ACTIVE_DATE_HERE>",
    inactiveDate: "<YOUR_INACTIVE_DATE_HERE>",
    certificateName: "<YOUR_CERTIFICATE_NAME_HERE>",
  });
  const updateCertificateResult = await as2Client.send(updateCertificateCommand);
  console.info(updateCertificateResult);
}

main();
```

### UpdateConnectorCommand

Update an existing AS2 connector.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  localProfileId?: string,
  partnerProfileId?: string,
  url?: string,
  messageSubject?: string,
  compression?: enum,
  encryptionAlgorithm?: enum,
  signingAlgorithm?: enum,
  mdnSigningAlgorithm?: enum,
  mdnResponse?: enum,
  connectorName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                     |                                                                                    |
| ------------------- | ---------------------------------------------------------------------------------- |
| compression         | Optional. Allowed values: `ZLIB`, `DISABLED`.                                      |
| connectorName       | Required.                                                                          |
| encryptionAlgorithm | Optional. Allowed values: `AES128_CBC`, `AES192_CBC`, `AES256_CBC`, `NONE`.        |
| idempotencyKey      | Optional.                                                                          |
| localProfileId      | Optional.                                                                          |
| mdnResponse         | Optional. Allowed values: `SYNC`, `NONE`.                                          |
| mdnSigningAlgorithm | Optional. Allowed values: `SHA256`, `SHA384`, `SHA512`, `SHA1`, `NONE`, `DEFAULT`. |
| messageSubject      | Optional.                                                                          |
| partnerProfileId    | Optional.                                                                          |
| signingAlgorithm    | Optional. Allowed values: `SHA256`, `SHA384`, `SHA512`, `SHA1`, `NONE`.            |
| url                 | Optional.                                                                          |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  connectorName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| connectorName                 |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  UpdateConnectorCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const updateConnectorCommand = new UpdateConnectorCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    localProfileId: "<YOUR_LOCAL_PROFILE_ID_HERE>",
    partnerProfileId: "<YOUR_PARTNER_PROFILE_ID_HERE>",
    url: "<YOUR_URL_HERE>",
    messageSubject: "<YOUR_MESSAGE_SUBJECT_HERE>",
    compression: <YOUR_COMPRESSION_HERE>,
    encryptionAlgorithm: <YOUR_ENCRYPTION_ALGORITHM_HERE>,
    signingAlgorithm: <YOUR_SIGNING_ALGORITHM_HERE>,
    mdnSigningAlgorithm: <YOUR_MDN_SIGNING_ALGORITHM_HERE>,
    mdnResponse: <YOUR_MDN_RESPONSE_HERE>,
    connectorName: "<YOUR_CONNECTOR_NAME_HERE>"
  });
  const updateConnectorResult = await as2Client.send(updateConnectorCommand);
  console.info(updateConnectorResult);
}

main();
```

### UpdateProfileCommand

Update an existing AS2 Profile.

#### Parameters

```javascript
{
  idempotencyKey?: string,
  certificateIds: [string],
  profileName: string
}
```

The constructor of the command takes an object as its only argument. All parameters are fields within this object.

|                |           |
| -------------- | --------- |
| certificateIds | Optional. |
| idempotencyKey | Optional. |
| profileName    | Required. |

#### Return value

```javascript
{
  resourceDetail: {
    status: enum,
    statusMessage: string,
    idempotencyKey: string,
    createdAt: timestamp,
    updatedAt: timestamp
  },
  profileName: string
}
```

The [`send()`](#send) method of the [As2Client](#as2client) will return the result of the command.

|                               |                                                                  |
| ----------------------------- | ---------------------------------------------------------------- |
| profileName                   |                                                                  |
| resourceDetail                |                                                                  |
| resourceDetail.status         | Allowed values: `AVAILABLE`, `UNDER_CHANGE`, `TAINTED`, `ERROR`. |
| resourceDetail.statusMessage  |                                                                  |
| resourceDetail.idempotencyKey |                                                                  |
| resourceDetail.createdAt      |                                                                  |
| resourceDetail.updatedAt      |                                                                  |

#### Example

```javascript
import {
  As2Client,
  UpdateProfileCommand
} from "@stedi/sdk-client-as2";

async function main() {
  const as2Client = new As2Client({
    region: "us",
    apiKey: process.env.STEDI_API_KEY
  });

  const updateProfileCommand = new UpdateProfileCommand({
    idempotencyKey: "<YOUR_IDEMPOTENCY_KEY_HERE>",
    certificateIds: [
      <YOUR_CERTIFICATE_IDS>
    ],
    profileName: "<YOUR_PROFILE_NAME_HERE>"
  });
  const updateProfileResult = await as2Client.send(updateProfileCommand);
  console.info(updateProfileResult);
}

main();
```

## As2Client

|                             |                                                                 |
| --------------------------- | --------------------------------------------------------------- |
| [constructor](#constructor) | Creates a client that allows you to send commands to Stedi AS2. |
| [destroy()](#destroy)       | Shuts down the client and its underlying resources.             |
| [send()](#send)             | Sends a command.                                                |

### constructor

Creates a client that allows you to send commands to Stedi AS2.

#### Parameters

```javascript
{
  region: string,
  apiKey: string
}
```

The constructor takes an object as its only argument. All parameters are fields within this object.

|        |                                                                                                                                                                                                                  |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| region | Required. The region that hosts your AS2 resources. At the moment, we only support `us`.                                                                                                                         |
| apiKey | Required. The [API key](/docs/accounts-and-billing/authentication) used to authenticate your requests. You can create a client without specifying `apiKey`, but if you do, calls to [`send()`](#send) will fail. |

#### Return value

An instance of the `As2Client` class.

#### Exceptions

|       |                      |
| ----- | -------------------- |
| Error | `region` is missing. |

#### Example

```javascript
import { As2Client } from "@stedi/sdk-client-as2";

const as2Client = new As2Client({
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
import { As2Client } from "@stedi/sdk-client-as2";

const as2Client = new As2Client({
  region: "us",
  apiKey: process.env.STEDI_API_KEY,
});

as2Client.destroy();
```

### send

Sends a command to Stedi AS2.

#### Parameters

```javascript
{
  command: Command;
}
```

`command` must be an instance of one of the [valid AS2 commands](#commands).

#### Return value

Depends on the command you sent. Check the description of the command you’re interested in.

#### Exceptions

Depends on the command you sent. Check the description of the command you’re interested in.
