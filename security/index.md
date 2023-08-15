---
title: Data & Privacy
slug: data-privacy
description: "Data & Privacy breakdown for Stedi services"
---

## Definitions

- **Customer Data** – customer-owned information that is transmitted, processed, or persisted by a Stedi product; customer data is protected by strong security controls to ensure that it can only be accessed by customers. Customer data is always encrypted when transmitted over a network, and at rest.
- **Metadata** – information required for product configuration, request routing, operational visibility, and troubleshooting; this is needed for the correct functioning of Stedi systems and may be viewed by suitably authorized Stedi staff. Metadata required to assess the compliance of the systems is stored for 7 years.

## Our Assurance

- Only product **metadata** may be logged and readable to suitably authorized Stedi personnel
- **Customer data** is always encrypted in transit and at rest using unique service-managed keys. Stedi personnel are not able to read customer data.

## Table of all Stedi products

| **Stedi service**                          | **Metadata**                                                                                                                 | **Customer Data**                                                 | **What customer data are persisted?** |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------- | ------------------------------------- |
| Stedi.com users                            | First name, last name                                                                                                        | Account password                                                  | Email, account password               |
| Stedi Accounts                             | Account name, logo, url                                                                                                      | None                                                              | None                                  |
| Stedi API Keys (V1)                        | API key description, key prefix                                                                                              | Full API key (hashed)                                             | Hashed secret                         |
| API &amp; Access Keys (machine identities) | Identity names, Access Key identifiers                                                                                       | Secret access keys (hashed)                                       | None                                  |
| Functions\*                                | Function name, program code, environment variables                                                                           | Inputs, outputs, runtime memory, ephemeral storage, logged output | Functions log output                  |
| Core                                       | Bucket names, object key names                                                                                               | Object contents                                                   | Object contents                       |
| Mappings                                   | Size of the request, latency, account ID, mapping ID                                                                         | Payload                                                           | None                                  |
| SFTP                                       | Usernames, linked bucket names, source IP addresses, directories/filenames accessed over SFTP                                | None                                                              | None                                  |
| Buckets                                    | Bucket names, object key names                                                                                               | Object contents                                                   | Object contents                       |
| Stash                                      | Keyspace names                                                                                                               | Key-Value items                                                   | Key-Value items                       |
| AS2                                        | Server identifiers, channel identifiers, agreement identifiers                                                               | Certificate materials                                             | None                                  |
| EDI Translate                              | Size of the request, latency, transaction set, release, guide id, account id, number of transaction sets, number of segments | Payload                                                           | None                                  |

_\*For Functions, Stedi provides a runtime that executes customer code. Stedi is responsible for keeping the function underlying runtime up to date, patched, and secured. Customers are responsible for the secure development and updates of their own code._
