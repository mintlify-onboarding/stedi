---
title: AS2
slug: legacy/as2
product: as2
description: "Send and receive messages using the AS2 protocol."
lifecycle: legacy
searchable: true
childPageOrder:
  - as2/setup
  - as2/sending-and-receiving
  - as2/limits
  - as2/sdk
---

> **Important:** We recommend configuring AS2 connections from within [Stedi Core](/docs/core/configuration/connections). Legacy AS2 is not enabled in your account by default. Please [request access](/app/as2) and we will enable it for you.

The AS2 protocol lets you securely exchange encrypted files with your trading partners over HTTP. It uses digital certificates to authenticate the sender and protect the data in transit.

You can configure and use Stedi AS2 via the Stedi CLI, the [Stedi AS2 SDK](/docs/legacy/as2/sdk), or [the UI](/app/as2).

# Basics

Stedi AS2 has two key concepts: _Connectors_ and _Servers_.

If you only need to **send** AS2 messages, then you only need to configure a `connector`. Connectors are billed on a pure [usage basis](/pricing#as2), according to the number of messages sent (with an additional cost when an individual message exceeds 50mb) – there is no fixed cost associated with a connector.

If you only need to **receive** AS2 messages. then you only need to set up a `server`. Servers are billed both on a usage basis – again, according to the number and size of messages received – as well as on an hourly basis. You typically can use a single server for all of your trading partners, and both test and production connections.

Of course, if you need to both send **and** receive AS2 messages, you will need both a connector and a server.

Whether you're setting up a connector, a server, or both, you must:

- Create your signing and encryption certificates
- Collect public keys from your trading partner
- Import all certificates into Stedi AS2
- Create AS2 profiles for you and your partner.

Once those steps are complete, you can create a connector or configure a server.

# Prerequisites

The following resources are required to configure Stedi AS2.

## Stedi CLI

Install the Stedi CLI.

```shell
npm install --global @stedi/cli@latest
```

If you've installed the CLI previously, be sure to update it to the latest version. The easiest way to do this is by running the same installation command again.

## API key

You must pass a Stedi API key to AS2 with each command. We recommend storing your API key in a local environment variable. You can also use the `-a` option to manually pass the API key to the CLI, but the rest of this page assumes you are using an environment variable.

1. [Create a Stedi API key](https://www.stedi.com/app/settings/api-keys) and copy it to your clipboard. You can also use an existing API key, if available.

1. Create a local `.stedirc` file with the following contents.

```
{
    "api-key": "<YOUR_API_KEY>"
}
```

> **Warning:** You should never commit `.stedirc` to source control.
