---
title: Accessing files
slug: legacy/sftp/accessing-files
product: sftp
description: "You can access the files uploaded to Stedi SFTP using Stedi Buckets."
searchable: true
lifecycle: legacy
childPageOrder: []
---

> **Important:** We recommend configuring Stedi-hosted SFTP connections from within [Stedi Core](/docs/core/configuration/connections).

You use Stedi SFTP to manage the users that have access to your SFTP storage. You use Stedi Buckets to access your SFTP storage programmatically.

When you create your first Stedi SFTP user, Stedi SFTP will automatically provision a bucket for you. Whenever someone uploads a file using SFTP, it’s stored in that bucket. Whenever someone downloads a file using SFTP, it’s retrieved from that bucket.

## Stedi Buckets

If you want to access the files in the bucket from code, you have to use Stedi Buckets. All you need is an [API key](https://www.stedi.com/app/settings/api-keys) and the name of the bucket. The bucket name is generated automatically. It’s part of the response whenever you create a new user, retrieve information about a user, or list all users.

You can access all files uploaded through Stedi SFTP using Stedi Buckets and you can access all files uploaded through Stedi Buckets using Stedi SFTP.

> See also\
> [Reference: Stedi Buckets Javascript SDK](/docs/sdk)\
> [Reference: Stedi Buckets Web API](/docs/api/legacy/buckets)

### Access rights

When you access files using Stedi Buckets, your API key determines the access rights and your API key gives you access to all files. Stedi SFTP users are not taken into account when you access files from code.

### Bucket notifications

You can use a bucket notification to invoke a Stedi function each time someone uploads a file. This allows you to kick off an automated workflow. For example, when a trading partner uploads an EDI file, the function can pick it up, call EDI Translate, and send the result to your internal API.

Bucket notifications are available for all your buckets, including the one generated by Stedi SFTP.

> See also\
> [Overview: Bucket notifications](/docs/legacy/buckets-classic/notifications)

## Example

```javascript
import { SftpClient, ListUsersCommand } from "@stedi/sdk-client-sftp";
import { BucketsClient, ReadBucketCommand } from "@stedi/sdk-client-buckets";

main();

async function main() {
  // Create a client that provides access to Stedi SFTP.
  const sftpClient = new SftpClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  // Retrieve all SFTP users in an attempt to get the name of the bucket Stedi
  // SFTP has created for us.
  const listUsersCommand = new ListUsersCommand({});
  const listUsersResult = await sftpClient.send(listUsersCommand);

  if (!listUsersResult.items?.length) {
    // If there are not SFTP users, we assume there is no SFTP bucket.
    console.info("SFTP bucket doesn't exist.");
    return;
  }

  // Get the bucket name from the first user. The bucket name is the same for
  // all users.
  const bucketName = listUsersResult.items[0].bucketName;

  // Create a client that provides access to Stedi Buckets.
  const bucketsClient = new BucketsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY,
  });

  // Retrieve information about the SFTP bucket.
  const readBucketCommand = new ReadBucketCommand({
    bucketName: bucketName,
  });
  const readBucketResult = await bucketsClient.send(readBucketCommand);
  const createdAt = new Date(readBucketResult.createdAt);

  // Display information about the SFTP bucket.
  console.info(
    `Bucket ${bucketName} ` + `was created on ${createdAt.toDateString()} ` + `at ${createdAt.toTimeString()}.`,
  );

  const notificationCount = readBucketResult.notifications?.functions?.length;
  if (!notificationCount) {
    console.info("It has no bucket notifications.");
  } else {
    console.info("Bucket notifications:");
    for (const notification of readBucketResult.notifications.functions) {
      console.info(`- ${notification.functionName}`);
    }
  }
}
```
