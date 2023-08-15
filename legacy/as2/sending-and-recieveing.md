---
title: Sending and receiving Messages
slug: legacy/as2/sending-and-receiving
product: as2
description: "Send and receive messages using the AS2 protocol."
lifecycle: legacy
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring AS2 connections from within [Stedi Core](/docs/core/configuration/connections). Legacy AS2 is not enabled in your account by default. Please [request access](/app/as2) and we will enable it for you.

## Transmitting files to your trading partner

With a connector created, you can now send a file to your trading partner. You can reference any file stored in a [Stedi Bucket](/docs/buckets), though we recommend creating a dedicated bucket for sending files via AS2 (and, if you're planning to receive files via AS2, a separate bucket for receiving files).

### Creating a bucket for outbound files

Bucket names must be globally unique (across all Stedi accounts). We recommend using the convention `as2-outbound-<STEDI_ACCOUNT_ID>` (your account ID can be found in [your account settings](/app/settings/account-details)), so if you were creating a bucket for account ID `be1b4859-9c75-475c-9197-268100542a42`, the name would be `as2-outbound-be1b4859-9c75-475c-9197-268100542a42`.

```shell
stedi buckets create-bucket --bucket-name <BUCKET_NAME>
```

### Uploading an outbound file

Next, you will need to upload the file you want to transmit. We recommend using the convention of creating a separate folder for each trading partner. If you're using [Stedi Core](/docs/core), you can use the [Partnership ID](/app/core/partnerships) as the directory.

```shell
stedi buckets put-object --bucket-name <BUCKET_NAME> \
 --key <BUCKET_FILE_PATH> \
 --body <LOCAL_FILE_PATH>

```

You can also do this via [the Buckets UI](/app/buckets) or [SDK](https://www.npmjs.com/package/@stedi/sdk-client-buckets).

### Transmitting the file

1. Transmitting a file requires the connector ID, not the connector name, so the first step is to retrieve the connector ID.

   ```shell
   stedi as2 list-connectors
   stedi as2 describe-connector --name <CONNECTOR_NAME>
   ```

1. Now you're ready to send the file to your trading partner.

   ```shell
   stedi as2 start-file-transfer --connector-id <CONNECTOR_ID> \
       --send-file-paths <FILE_PATH>
   ```

   If you followed the recommend bucket naming convention, the file path would look like this: `as2-outbound-be1b4859-9c75-475c-9197-268100542a42/this-is-me_another-merch/some_file.edi`

Note that every `start-file-transfer` attempt will return a `transferId`, so a `transferId` does not mean your message was successfully transmitted. For example, if you specify a file path that doesn't exist, you will still receive a `transferId`.

### Confirming that a file was transmitted successfully

When a transmission is successful, two files – a Message Disposition Notice (MDN) file and a JSON file – will be placed in the `/processed` folder of the base directory that you specified in the `start-file-transfer` Stedi CLI command or [Stedi AS2 SDK](/docs/legacy/as2/sdk) call. For example, if you followed the recommended naming conventions mentioned earlier:

```
stedi as2 start-file-transfer --connector-id <CONNECTOR_ID> \
  --send-file-paths as2-outbound-<STEDI_ACCOUNT_ID>/<PARTNERSHIP_ID>/some_file.edi
```

If you ran this command, a successful MDN and JSON file would be saved in `as2-outbound-<STEDI_ACCOUNT_ID>/<PARTNERSHIP_ID>/processed`.

- The JSON file is created and saved as `original_filename.transferId.messageId.original_extension.json`.
- The MDN file is created and saved as `original_filename.transferId.messageId.original_extension.mdn`.

For example, if there was an outbound file named `ExampleFileOutTestOutboundSyncMdn.dat`, the following files would be created:

- **JSON** – `ExampleFileOutTestOutboundSyncMdn.dedf4601-4e90-4043-b16b-579af35e0d83.fbe18db8-7361-42ff-8ab6-49ec1e435f34@c9c705f0baaaabaa.dat.json`
- **MDN** – `ExampleFileOutTestOutboundSyncMdn.dedf4601-4e90-4043-b16b-579af35e0d83.fbe18db8-7361-42ff-8ab6-49ec1e435f34@c9c705f0baaaabaa.dat.mdn`

If you do not see an MDN and JSON file in the `processed` folder that corresponds to your `transferId`, then your transmission was not successful.

### Troubleshooting outbound transmissions

When a transmission is not successful, a JSON file will be created in the `failed` folder.

The JSON file contains the following fields, which can be used to diagnose errors:

- `agreement-id`
- `as2-from`
- `as2-to`
- `as2-message-id`
- `transfer-id`
- `client-ip`
- `connector-id`
- `failure-message`
- `file-path`
- `message-subject`
- `mdn-message-id`
- `mdn-subject`
- `requester-file-name`
- `requester-content-type`
- `server-id`
- `status-code`
- `failure-code`
- `transfer-size`

# Receiving messages

Receiving AS2 messages requires a server and an agreement. You can use one server for all your trading partners, and you will need to create an agreement for each trading parnter

## Create a server

Create an AS2 server. The process may take several minutes to complete.

```shell
stedi as2 create-server --name <SERVER_NAME>
```

## Create an agreement

An agreement binds all the information required to receive AS2 messages.

- A local profile containing your certificates and your AS2 ID
- A partner profile containing your trading partner's certificates and their AS2 ID
- Your server
- The bucket and directory where you want to store incoming files

1. Retrieve the IDs of your local profile and the partner profile.

   ```shell
   stedi as2 list-profiles
   stedi as2 describe-profile --name <LOCAL_PROFILE>
   stedi as2 describe-profile --name <PARTNER_PROFILE>
   ```

1. Retrieve your AS2 server ID.

   ```shell
   stedi as2 list-servers
   stedi as2 describe-server --name <SERVER_NAME>
   ```

1. Create a bucket for inbound transmissions.

   You can reference any bucket, but we recommend creating a dedicated bucket for receiving files (and a separate bucket if you're planning to send files).

   Bucket names must be globally unique (across all Stedi accounts). We recommend using the convention `as2-inbound-<STEDI_ACCOUNT_ID>` (your account ID can be found in [your account settings](/app/settings/account-details)), so if you were creating a bucket for account ID `be1b4859-9c75-475c-9197-268100542a42`, the name would be `as2-inbound-be1b4859-9c75-475c-9197-268100542a42`.

   ```shell
   stedi buckets create-bucket --bucket-name <BUCKET_NAME>
   ```

1. Create an agreement.
   You must use a single path to specify the bucket and a folder in that bucket. We recommend using the convention of creating a separate folder for each trading partner. If you're using [Stedi Core](/docs/core), you can use the [Partnership ID](/app/core/partnerships) as the directory. For example, to specify the path for `as2-inbound-be1b4859-9c75-475c-9197-268100542a42` you would use `as2-inbound-be1b4859-9c75-475c-9197-268100542a42/a-partnership-id`.

   ```shell
   stedi as2 create-agreement \
       --name <PARTNER> \
       --base-directory "/<BUCKET_NAME>/<PARTNERSHIP>" \
       --description "" \
       --local-profile-id <LOCAL_PROFILE_ID> \
       --partner-profile-id <PARTNER_PROFILE_ID> \
       --server-id <SERVER_ID> \
       --status ACTIVE
   ```

## Send your server URL to your partner

In order for your trading partner to send you AS2 transmissions, you will need to send them your public keys, your AS2 ID, and your server URL.

You should already have your public keys and AS2 ID handy from earlier steps. To obtain the server URL:

1. Retrieve your server details.

   ```shell
   stedi as2 describe-server --name <SERVER_NAME>
   ```

1. Find the `serverUrl` field and add `http://` in front of it and `:5080` after it. For example: `http://s-d1bfcba6f26349968.server.transfer.us-east-1.amazonaws.com:5080`.

## Troubleshooting inbound transmissions

- If an incoming file is processed successfully, the file (and the corresponding JSON file) is saved to the `/processed` folder of the base directory specified in the agreement. For example, `as2-inbound-be1b4859-9c75-475c-9197-268100542a42/a-partnership-id/processed`.
- The transferred file is stored in the `processed` folder as `original_filename.messageId.original_extension`. That is, the message ID for the transmission is appended to the name of the file, before its original extension.
- A JSON file is created and saved as `original_filename.messageId.original_extension.json`. In addition to the message ID being added, the string `.json` is appended to the transferred file's name. The JSON file contains the following fields:

  - `agreement-id`
  - `as2-from`
  - `as2-to`
  - `as2-message-id`
  - `transfer-id`
  - `client-ip`
  - `connector-id`
  - `failure-message`
  - `file-path`
  - `message-subject`
  - `mdn-message-id`
  - `mdn-subject`
  - `requester-file-name`
  - `requester-content-type`
  - `server-id`
  - `status-code`
  - `failure-code`
  - `transfer-size`

- A Message Disposition Notice (MDN) file is created and saved as `original_filename.messageId.original_extension.mdn`. In addition to the message ID being added, the string `.mdn` is appended to the transferred file's name.
- If there is an inbound file named `ExampleFileInBucketPayload.dat`, the following files are created:
  - **File** – `ExampleFileInBucketPayload.c4d6b6c7-23ea-4b8c-9ada-0cb811dc8b35@44313c54b0a46a36.dat`
  - **JSON** – `ExampleFileInBucketPayload.c4d6b6c7-23ea-4b8c-9ada-0cb811dc8b35@44313c54b0a46a36.dat.json`
  - **MDN** – `ExampleFileInBucketPayload.c4d6b6c7-23ea-4b8c-9ada-0cb811dc8b35@44313c54b0a46a36.dat.mdn`
- If an incoming file cannot be processed successfully, the file (and the corresponding JSON file) is saved to the `/failed` folder. For example, `as2-inbound-be1b4859-9c75-475c-9197-268100542a42/a-partnership-id/failed`.

- The following inbound encryption ciphers are supported: `AES128_CBC`, `AES192_CBC`, `AES256_CBC`, `3DES` (note: this is supported for inbound transmissions for backward compatibility only; it is not supported when sending AS2 transmissions from Stedi)

# Delete AS2 Resources

Delete all of the AS2 resources.

```shell
stedi as2 delete-connector --name <CONNECTOR_NAME>
stedi as2 delete-agreement --name <AGREEMENT_NAME>
stedi as2 delete-profile --name <PARTNER_PROFILE_NAME>
stedi as2 delete-profile --name <LOCAL_PROFILE_NAME>
stedi as2 delete-certificate --name <PARTNER_SIGNING_CERT>
stedi as2 delete-certificate --name <PARTNER_ENCRYPTION_CERT>
stedi as2 delete-certificate --name <YOUR_PUBLIC_SIGNING_CERT>
stedi as2 delete-certificate --name <YOUR_PRIVATE_SIGNING_CERT>
stedi as2 delete-certificate --name <YOUR_PUBLIC_ENCRYPTION_CERT>
stedi as2 delete-certificate --name <YOUR_PRIVATE_ENCRYPTION_CERT>
```

Delete your AS2 server.

> **Warning:** Make sure no other trading partners are using the server before you delete it.

```shell
stedi as2 delete-server --name <SERVER_NAME>
```
