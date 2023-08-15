---
title: Notification message
slug: legacy/buckets-classic/notification-message
product: ""
description: "Reference for the message that Stedi Buckets sends to Stedi Functions."
searchable: true
---

A bucket can send a notification to a function running on Stedi when an event occurs. The function will get a message as its first argument with information about the event.

```javascript
{
  Records: [{
    eventName: string,
    s3: {
      bucket: {
        name: string
      },
      object: {
        key: string,
        size: number
      }
    }
  }]
}
```

| | |
|-|-|
Records | The notifications sent by Stedi Buckets.
eventName | The name of the event that caused the notification. See below for a [list of supported events](#events).
s3.bucket.name | The name of the bucket that received a new object.
s3.object.key | The key of the object that was added to the bucket. The key is URL-encoded, so you probably need to [decode it](#decoding-the-key) first.
s3.object.size | The size in bytes of the object that was added to the bucket.

The message may contain additional fields, but you shouldn’t rely on there presence nor their value.

## Events

| | |
|-|-|
ObjectCreated:Put | A new object was added to the bucket.

Even though Stedi Buckets currently only supports one event, you should always check the event before processing the notification. We may add notifications for other events in the future and you don’t want to end up running your code for events it wasn’t designed for.

## Decoding the key

The object key is URL-encoded. Most likely, you want to use the key with Stedi Buckets and Stedi Buckets expects it to not be encoded. The following function shows how to decode the key.

```javascript
function decodeKey(key) {
  // JavaScript’s decodeURIComponent() doesn’t bother converting pluses to
  // spaces, so we’ll have to do that in a separate step.
  key = key.replaceAll("+", " ");

  // Decode all the escaped characters.
  return decodeURIComponent(key);
}
```