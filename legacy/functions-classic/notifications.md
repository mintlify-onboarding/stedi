---
title: Notifications
slug: legacy/functions-classic/notifications
product: functions-classic
description: "You can invoke a Stedi function automatically when certain events occur in other Stedi products."
searchable: false
---

You can invoke a Stedi function automatically when certain events occur in other Stedi products. This allows you to kick off an automated workflow. For example, when a trading partner uploads an EDI message to a bucket, the function can pick it up, call EDI Translate, and send the result to your internal API.

## Stedi Buckets

Stedi Buckets is the only product that supports sending notifications right now. It can invoke a function whenever an object is added to a bucket. You enable these notifications by configuring the bucket that sends them. You don’t need to configure anything on the side of the function.

> See also\
> [Overview: Bucket notifications](/docs/buckets/notifications)\
> [Reference: Update bucket notifications with the JavaScript SDK](/docs/buckets/sdk#updatecommandobject)\
> [Reference: Update bucket notifications with the Web API](/docs/api/legacy/buckets#UpdateBucket)
