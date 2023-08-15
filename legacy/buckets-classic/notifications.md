---
title: Bucket notifications
sidebarTitle: Notifications
slug: legacy/buckets-classic/notifications
product: ""
description: "You can use a bucket notification to invoke a Stedi function each time an object is added to your bucket."
searchable: true
---

You can use a bucket notification to invoke a Stedi function each time an object is added to your bucket. This allows you to kick off an automated workflow. For example, when a trading partner uploads an EDI message, the function can pick it up, call EDI Translate, and send the result to your internal API.

> **⚠ Warning:**
> If you have a function that receives bucket event notifications and writes output to a bucket, we recommend using different buckets for the input and the output. This approach reduces the risk of creating an infinite loop where writes continue to trigger additional function invocations.

You can enable bucket notifications for any bucket in your account, including the bucket created by Stedi SFTP.

## Creating a bucket notification

Before you can enable notifications on a bucket, you need to [write a Stedi function that can receive the notification](#writing-a-function). Once that’s done, you need to let the bucket know the name of that function.

- [Create a notification using the JavaScript SDK](https://www.stedi.com/docs/legacy/buckets-classic/sdk#updatebucketcommand)
- [Create a notification using the web API](https://www.stedi.com/docs/api/legacy/buckets#UpdateBucket)
- [Create a notification using the UI](https://www.stedi.com/app/buckets)

A bucket can have multiple notifications configured, so multiple functions can be invoked simultaneously when a new object is added.

When you update a bucket’s notification configuration, the new configuration will overwrite the previous one. If you want to keep the previous notifications, you need to include them in the new configuration.

## Writing a function

The Stedi function invoked by Stedi Buckets will receive a message as its first argument. The message contains information about the added object and looks something like this.

```json
{
  "Records": [{
    "eventName":"ObjectCreated:Put",
    "s3": {
      "bucket": {
        "name": "demo-notification-input"
      },
      "object": {
        "key": "test.txt"
        "size": 1493
      }
    }
  }]
}
```

> See also\
> [Reference: Notification message](/docs/buckets/notification-message)

As you can see, the message contains an array of notifications, called `Records`. While it’s rare for multiple notifications to be sent at once, you should keep the possibility in mind when writing your function.

```javascript
export async function handler(event) {
  const notifications = event.Records;
  for (const notification of notifications) {
    console.info(notification.s3.bucket.name);
    console.info(notification.s3.object.key);
  }
}
```

> See also\
> [Tutorial: Writing a basic Stedi function](https://github.com/Stedi-Demos/simple-function)

`eventName` tells you the reason for sending the notification. At the moment, Stedi Buckets only sends notifications when an object is added, so this will always be `ObjectCreated:Put`. We may add notifications for other events in the future, so we recommend you check `eventName` to future-proof your function.

```javascript
const notifications = event.Records.filter((record) => record.eventName === "ObjectCreated:Put");
```

The bucket notification doesn’t contain any kind of filter. For example, you can’t trigger a notification only when the key starts with `incoming/`. Whenever an object is added to the bucket, the function will be invoked. If you need a filter, you need to include it in your function.

```javascript
const notifications = event.Records.filter((record) => record.eventName === "ObjectCreated:Put");
filter((record) => record.s3.object.key.startsWith("incoming/"));
```

When filtering on a prefix like this, keep in mind that keys can start with a leading `/`. For example, `/my.file` is the same as `my.file`. It’s best to filter for both.

```javascript
const notifications = event.Records.filter((record) => record.eventName === "ObjectCreated:Put");
filter((record) => record.s3.object.key.startsWith("incoming/") || record.s3.object.key.startsWith("incoming/"));
```

Stedi SFTP never adds a leading `/` to the key, despite the fact that it shows home directories with a leading `/`.

## Notes

- You can only send a notification to Stedi Functions. If you want to pass the added object to a different Stedi product, you have to write a function to do that for you.
