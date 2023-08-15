---
title: What is a bucket?
slug: cloud/buckets/what-is-a-bucket
product: ""
description: "Stedi Buckets allows you to store files in a bucket."
searchable: true
---

A bucket is a place where you can store large data objects. The object has a key, which you can use to refer to the object. In practice, most people think about objects and keys as files and file paths.

## Storage

All data you store in a bucket is encrypted at rest. The data will persist in your bucket, unless you actively delete it.

## Scalable

You don’t have to worry about storage space when working with buckets; you’ll never run out. The same goes for bandwith: you can upload and download data as much as you need.

## Simulating directories

A bucket doesn’t have the concept of directories. All objects are stored in a flat collection.

<!---
  DIAGRAM
  On the left, a directory structure with a bunch of files. On the right, the same files, but as a flat list of keys.
-->

The key of an object can contain any character, including the `/`. This is often used to simulate a directory structure. For example, if the key is `/documents/instructions`, you can pretend the object is a file with the name `instructions` inside the directory `documents`. Stedi SFTP uses this trick to simulate a directory structure.

This does lead to subtle differences between directory structures and buckets. For example, you can’t have an empty directory in a bucket. You can solve this by creating an object with the directory name as the key, but then you need to make sure you don’t treat the directory as if it is a file.

<!---
  TODO
  Add link to an article about the difference between files and objects.
-->

## Using buckets in your workflow

A bucket often serves as a source of input for another Stedi product, or as a place to store its output. For example, an EDI file stored in a bucket is passed to EDI Core and the resulting JSON gets stored in a bucket.

This requires a bit of coordination. You can write a function that triggers whenever a new EDI file comes in. It passes the file on to EDI Core and waits for output. It then writes the output to a bucket.

To assist with building event-driven workflows, Buckets supports sending notifications when objects in them change. You can connect a function to receive these events and it will be invoked whenever something happens to an object in the bucket. You should take extra care when creating new objects in response to a bucket event. If your event handler function writes to the same bucket as it is listening to, this can cause a loop where it triggers more executions of itself. In order to avoid this, use a separate bucket for storing the output data. If you need to store the output in the same bucket, you can use different object prefix paths and explicitly check that the path of an incoming event requires a response in your event handler code.

<!---
  TODO
  Add a link to an article about reading and writing files from Functions.
-->

## Security

Buckets belong to a Stedi-account. Every member of the account has access to all buckets in the account and to all objects inside those buckets. When you write code that accesses Stedi Buckets, you provide an [API key](https://www.stedi.com/app/settings/api-keys) to prove that your code is allowed to access all buckets and objects in your account.

If you want to give people outside your account access to some objects but not all of them, then you can use [Stedi SFTP](/docs/legacy/sftp).
