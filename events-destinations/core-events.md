---
title: Stedi Core Events
sidebarTitle: Core event types
slug: events/event-types
description: "Stedi Core outputs EDI processing events at three levels: per file, per functional group, and per transaction. You can consume these events and use them to automate your processing pipeline and integrate Stedi with external systems and business applications."
---

Stedi Core events are split into three different levels, each representing a different level of granularity. Events are also categorized by their result states, representing success or failure during processing.

## Event levels

1. **file:** Events with information about a file Core has detected. File events contain summary information about the data type in the file, like X12 EDI, JSON, CSV, XML, etc.
1. **functional_group:** Events with information about each [Functional Group](https://www.stedi.com/edi/x12/element/479) within an X12 EDI file.
1. **transaction:** Events with information about a single transaction within an X12 EDI file.

## Event categories

1. **processed:** Events are emitted for each event level, but only when Core has processed the entire file successfully.
1. **failed:** Events are only emitted at the **file** level because Core stops processing a file immediately when it finds an invalid transaction set.

## When are events emitted?

Stedi emits events when it has fully completed processing a file.

### When an entire file has been successfully processed

Consider an EDI file that contains two functional groups, and a total of three transaction sets. When Stedi successfully processes all transaction sets, it generates a total of six events:

- 1x `file.processed` - indicating the entire file was processed successfully.
- 2x `functional_group.processed` - one event for each group within the file.
- 3x `transaction.processed` - one event for each transaction set found in both groups.

<picture>
  <source srcSet="/images/docs/core/successful-interchange.png" />

  <img alt="X12 File where all Transaction Sets are processed successfully" src="/images/docs/core/successful-interchange.png" />
</picture>

### When at least one transaction fails to validate

If one or more transaction sets in a file fails validation, Stedi emits a single `file.failed` error, indicating that one or more transactions failed to process. Stedi cannot process the entire file until you address those issues.

<picture>
  <source srcSet="/images/docs/core/failed-interchange.png" />

  <img alt="X12 File where one Transaction Set fails validation, blocking the entire file" src="/images/docs/core/failed-interchange.png" />
</picture>

## Event structure

All events Stedi generates follow a standard JSON schema. The event payload itself never includes the contents of a given file or transaction. Instead, it references a [Stedi bucket](/docs/cloud/buckets) and provides a path to retrieve the entire object.

**Event JSON Structure**

```json
{
  "version": "0",
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "file.processed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": { ... }
}
```

In addition to their `version`, `id`, and `time`, events have the following fields:

- **`detail-type`** - Indicates the type of event, such as `transaction.processed` or `file.failed`
- **`source`** - Indicates the component that generated the event. All Core events use `source: “stedi.core”`.
- **`detail`** - The event-specific JSON payload. The schema for each payload is determined by the `detail-type`.

## Available events

### file.created

Stedi emits one `file.created` event for every new object added to a [Stedi bucket](/docs/cloud/buckets). You may want to consume these events when files arrive in a bucket outside of Core.

```json
{
  "source": "stedi.buckets",
  "detail-type": "file.created",
  "detail": {
    "metadata": {
      "processedAt": "2023-03-14T19:19:17.950Z"
    },
    "source": {
      "bucketName": "stedi-bucket-name",
      "key": "filename",
      "size": 31415
    }
  }
}
```

### transaction.processed

The `transaction.processed` event is the primary integration point for building end-to-end EDI systems on Stedi. Stedi emits one `transaction.processed` event for every transaction successfully processed.

```json
{
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "transaction.processed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
    "version": "2023-02-13",
    "direction": "SENT",
    "envelopes": {
      "interchange": {
        "acknowledgmentRequestedCode": "0",
        "controlNumber": 1746,
        "date": "2022-09-14",
        "receiverId": "THISISME",
        "receiverQualifier": "ZZ",
        "senderId": "ANOTHERMERCH",
        "senderQualifier": "ZZ",
        "time": "20:22",
        "usageIndicatorCode": "T",
        "versionNumberCode": "00501"
      },
      "functionalGroup": {
        "applicationReceiverCode": "THISISME",
        "applicationSenderCode": "ANOTHERMERCH",
        "controlNumber": 1746,
        "date": "2022-09-14",
        "functionalIdentifierCode": "PR",
        "release": "005010",
        "responsibleAgencyCode": "X",
        "time": "20:22:22"
      }
    },
    "metadata": {
      "processedAt": "2023-03-14T19:19:17.950Z"
    },
    "transaction": {
      "id": "a542c152-87c5-4916-848d-e60d47fe0e34",
      "controlNumber": 1,
      "transactionSetIdentifier": "855",
      "guideId": "some-uuid"
    },
    "input": {
      "type": "EDI/X12",
      "bucketName": "xxxxxxx-sftp",
      "key": "trading-partner/xxxx/xxxx.edi"
    },
    "output": {
      "type": "STEDI/GUIDE-JSON",
      "bucketName": "stedi-default-engine-artifacts-xxxxx",
      "key": "123ABC/1746-1746-1.json"
    },
    "partnership": {
      "partnershipId": "ThisIsMe-AnotherMerch",
      "sender": {
        "isa": {
          "qualifier": "ZZ",
          "id": "ANOTHERMERCH"
        },
        "profileId": "321"
      },
      "receiver": {
        "isa": {
          "qualifier": "ZZ",
          "id": "THISISME"
        },
        "profileId": "123"
      }
    }
  }
}
```

### functional_group.processed

You may want to consume `functional_group.processed` events when generating 997 Functional Acknowledgments in response to incoming EDI files.

```json
{
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "functional_group.processed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
    "version": "2023-02-13",
    "direction": "RECEIVED",
    "envelopes": {
      "interchange": {
        "acknowledgmentRequestedCode": "0",
        "controlNumber": 1746,
        "date": "2022-09-14",
        "receiverId": "THISISME",
        "receiverQualifier": "ZZ",
        "senderId": "ANOTHERMERCH",
        "senderQualifier": "ZZ",
        "time": "20:22",
        "usageIndicatorCode": "T",
        "versionNumberCode": "00501"
      },
      "functionalGroup": {
        "applicationReceiverCode": "THISISME",
        "applicationSenderCode": "ANOTHERMERCH",
        "controlNumber": 1746,
        "date": "2022-09-14",
        "functionalIdentifierCode": "PR",
        "release": "005010",
        "responsibleAgencyCode": "X",
        "time": "20:22:22"
      }
    },
    "transactionSetCount": 1,
    "transactionSetIds": ["855", "850"],
    "input": {
      "type": "EDI/X12",
      "bucketName": "chain-test",
      "key": "855.edi"
    },
    "partnership": {
      "partnershipId": "ThisIsMe-AnotherMerch",
      "sender": {
        "isa": {
          "qualifier": "ZZ",
          "id": "ANOTHERMERCH"
        },
        "profileId": "321"
      },
      "receiver": {
        "isa": {
          "qualifier": "ZZ",
          "id": "THISISME"
        },
        "profileId": "123"
      }
    }
  }
}
```

### file.processed

You may want to consume `file.processed` events when you want to route non-EDI files that arrive on Stedi for additional processing.

Core returns a `file.processed` event for all files with `.json`, `.csv`, `.xml`, `.xls`, `.xlsx` or `.pdf` file extensions and does not attempt to parse them as EDI. You can view details about the file execution in Core.

```json
{
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "file.processed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
    "metadata": {
      "processedAt": "2023-03-14T19:19:17.950Z"
    },
    "source": {
      "type": "CSV",
      "bucketName": "test-bucket",
      "key": "123ABC/1746-1746-1.txt",
      "size": 4301
    }
  }
}
```

### file.delivered

Core emits the `file.delivered` event every time a file is successfully delivered to a [connection](/docs/core/configuration/connections) for an outbound transaction set. This event is only emitted for generated EDI (outbound) files and not emitted for inbound files.

```json
{
  "id": "xxx-1234-b198-4321-7ededc63b103",
  "detail-type": "file.delivered",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
    "version": "2023-02-13",
    "metadata": {
      "processedAt": "2023-03-14T19:19:17.950Z"
    },
    "source": {
      "type": "bucket",
      "bucketName": "xxxxxxx",
      "key": "trading-partner/xxxx/xxxx.edi"
    },
    "connection": {
      "connectionType": "BUCKET",
      "connectionId": "ff42c152-87c5-4916-848d-e60d47fe0e21"
    },
    "delivery": {
      "status": "DELIVERED",
      "message": "File was delivered to 'some-bucket/path/a-file.edi'",
      "artifactId": "c5d6835f-24c4-4bed-8c1b-7e199d6d0f39"
    }
  }
}
```

### file.failed

Core emits `file.failed` events in two scenarios:

- When a failure occurs while processing an inbound file. Core emits the `file.failed` event at the file level. If a single transaction set fails, the entire file fails.
- When a Core cannot deliver an outbound file to a configured connection.

```json
{
  "version": "0",
  "id": "xxx-05d4-b198-2fde-7ededc63b103",
  "detail-type": "file.failed",
  "source": "stedi.core",
  "time": "2021-11-12T00:00:00Z",
  "detail": {
    "version": "2023-02-13",
    "fileId": "123ABC",
    "direction": "RECEIVED",
    "envelopes": {
      "interchange": {
        "acknowledgmentRequestedCode": "0",
        "controlNumber": 1746,
        "date": "220914",
        "receiverId": "THISISME",
        "receiverQualifier": "ZZ",
        "senderId": "ANOTHERMERCH",
        "senderQualifier": "ZZ",
        "time": "2022",
        "usageIndicatorCode": "T",
        "versionNumberCode": "00501"
      }
    },
    "input": {
      "type": "EDI/X12",
      "bucketName": "test-bucket",
      "key": "855.edi"
    },
    "errors": [
      {
        "message": "some message here",
        "faultCode": "UNKNOWN_ERROR"
      }
    ]
  }
}
```
