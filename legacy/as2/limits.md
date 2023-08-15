---
title: Limits
slug: legacy/as2/limits
product: as2
lifecycle: legacy
description: ""
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring AS2 connections from within [Stedi Core](/docs/core/configuration/connections). Legacy AS2 is not enabled in your account by default. Please [request access](/app/as2) and we will enable it for you.

| Description                              | Default limit | Can be raised |
| ---------------------------------------- | ------------- | ------------- |
| Number of AS2 servers                    | 1             | Yes           |
| Rate of AS2 messages received per server | 5 per second  | Yes           |
| Rate of messages sent per connector      | 3 per second  | No            |
| Connectors per account                   | 100           | Yes           |
| Agreements per server                    | 100           | Yes           |
| Certificates per account                 | 1,000         | Yes           |
| Maximum inbound AS2 message size         | 50 MB         | Yes           |
| Maximum outbound AS2 message size        | 50 MB         | Yes           |

## Technical specifications

| Capability                      | Inbound: [Receiving](/docs/legacy/as2/sending-and-receiving#receiving-messages) with Server/Agreement | Outbound: [Sending](/docs/legacy/as2/setup#sending-messages) with Connector |
| ------------------------------- | ----------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| TLS Encrypted Transport (HTTPS) | Yes                                                                                                   | Yes                                                                         |
| Synchronous MDN                 | Yes                                                                                                   | Yes                                                                         |
| Asynchronous MDN                | Yes                                                                                                   | No                                                                          |
| Message compression[^1]         | Yes                                                                                                   | Yes                                                                         |
| Static IP address               | No                                                                                                    | Yes                                                                         |
| Multiple file attachments       | Yes                                                                                                   | No                                                                          |
| Custom subject per AS2 message  | N/A                                                                                                   | No                                                                          |

[^1]: The only supported compression algorithm is ZLIB.

### Certificate requirements

Certificates with the following cryptographic algorithms and key sizes are supported:

- 2048-bit RSA
- 4096-bit RSA
- Elliptic Prime Curve 256 bit
- Elliptic Prime Curve 384 bit
- Elliptic Prime Curve 521 bit
