---
title: Setup
slug: legacy/as2/setup
product: as2
description: "Setup for AS2"
lifecycle: legacy
searchable: true
childPageOrder: []
---

> **Important:** We recommend configuring AS2 connections from within [Stedi Core](/docs/core/configuration/connections). Legacy AS2 is not enabled in your account by default. Please [request access](/app/as2) and we will enable it for you.

# Certificates and keys

A certificate consists of both a public key and a private key. There are two types of certificates:

- **Signing certificate:** This certificate allows you and your partners to verify the message sender's identity.
- **Encryption certificate:** This certificate allows you and your trading partners to encrypt and decrypt messages.

Some partners require two certificates, one for signing and one for encryption. Others may want you to to use the same certificate for both tasks. For example, Walmart's AS2 portal only asks you to upload a single certificate. Follow the instructions from your trading partner when defining your AS2 configuration.

If either you or your partner are using a certificate issued by a third party Certificate Authority (CA), you can skip the certificate creation steps and import the provided certificate and key directly. Note though that the format in which the encryption materials are provided to you may be different: Stedi AS2 requires them to be in PEM format. OpenSSL contains utilities to convert certificates and keys among different formats.

Certificates with the following cryptographic algorithms and key sizes are supported:

- 2048-bit RSA
- 4096-bit RSA
- Elliptic Prime Curve 256 bit
- Elliptic Prime Curve 384 bit
- Elliptic Prime Curve 521 bit

## Create your signing and encryption certificates

The following command creates two files: a private key and a public key.

If your trading partner requires unique certificates for signing and encryption, run the command twice. For each run, change the key names to indicate whether they are for signing or encryption. For example, use `acme-signing-private.pem` for the first run and `acme-encryption-private.pem` for the second run.

1. Create a new certificate. The `-days` option specifies the number of days the certificate will remain valid.

   ```shell
   openssl req -x509 -newkey rsa:4096 -keyout <PRIVATE_KEY_NAME>.pem \
       -out <PUBLIC_KEY_NAME>.pem -sha256 -days 365 -nodes
   ```

1. Enter information to identify your organization. You don’t need to fill out all fields, but you should specify what you can.

## Exchange public keys

After you create a certificate, you need to send your public key to your trading partner and your trading partner needs to send you their public key.

## Import your certificates

Upload your certificates to Stedi AS2.

- Use the `--usage` attribute to indicate whether the certificate is for signing or encryption. Valid values are `SIGNING` and `ENCRYPTION`.
- We recommend naming your signing certificate using the convention `<PARTNER_NAME>-<CERTIFICATE_USAGE>`, so if you're importing a signing certificate for your own company, ACME, the name would be `acme-signing`.
- A description is required. Note that it can only consist of alphanumeric characters, dashes, or underscores; you cannot use spaces in it. You can also leave it empty.
- If your trading partner requires a single certificate, upload the same certificate twice, once for each usage type.
- If you are using a certificate issued by a Certificate Authority (CA) rather than a self-signed certificate, you may need to also provide the certificate signature chain using the `--certificate-chain` option. The certificate chain is the list of certificates that can be used to verify the certificate's authenticity.

Upload your signing certificate.

```shell
stedi as2 import-certificate --name <SIGNING_CERT_NAME> \
    --description "" \
    --usage SIGNING \
    --private-key file://<PRIVATE_KEY_NAME>.pem \
    --certificate file://<PUBLIC_KEY_NAME>.pem
```

Upload your encryption certificate.

```shell
stedi as2 import-certificate --name <ENCRYPTION_CERT_NAME> \
    --description "" \
    --usage ENCRYPTION \
    --private-key file://<PRIVATE_KEY_NAME>.pem \
    --certificate file://<PUBLIC_KEY_NAME>.pem
```

### Alternative: Import certificates with a certificate chain

If you obtained a certificate from a Certificate Authority, you must provide the certificate chain. Upload your signing and encryption certificates along with the appropriate signing chain bundle using the `--certificate-chain` option.

```shell
stedi as2 import-certificate --name <ENCRYPTION_CERT_NAME> \
    --description <DESCRIPTION> \
    --usage <USAGE_TYPE> \
    --private-key file://<PRIVATE_KEY_NAME>.pem \
    --certificate file://<PUBLIC_KEY_NAME>.pem \
    --certificate-chain file://<CHAIN>.pem
```

## Import partner certificates to AS2

You also need to upload your trading partner's signing and encryption certificates to your Stedi account in order to verify and decrypt their messages. If your trading partner sends their certificates in binary format, you must [convert them to PEM](https://aboutssl.org/convert-certificate-to-pem-crt-to-pem-crt-to-pem-der-to-pem/) before uploading them. If your trading partner has only provided you with a single certificate, you can use the same certificate for both signing and encryption by importing the same certificate twice, once for each usage type.

1. If necessary, convert the certificate to PEM format.

   ```shell
   openssl x509 -inform der -in <CERT_NAME>.cer  -out <CERT_NAME>.pem
   ```

1. Save the certificates in PEM files. We recommend naming them using the convention `<PARTNER_NAME>-<CERTIFICATE_USAGE>` (e.g. `acme-signing.pem`). Your trading partner will only send you their public keys, so you don't need to include `public` in the file name. However, if your partner uses separate keys for signing and encryption, choose file names that clearly specify the usage.

1. Import the certificates to your Stedi account. If your partner uses the same certificate for both signing and encryption, use the same file in both commands.

- We recommend naming your signing certificate using the convention `<PARTNER_NAME>-<CERTIFICATE_USAGE>`, so if you're importing a signing certificate for your own company, ACME, the name would be `acme-signing`.
- A description is required. Note that it can only consist of alphanumeric characters, dashes, or underscores; you cannot use spaces in it. You can also leave it empty.

  ```shell
  stedi as2 import-certificate \
      --name partner-to-me-signing \
      --usage SIGNING \
      --description "" \
      --certificate file://<PUBLIC_KEY_NAME>.pem

  stedi as2 import-certificate \
      --name partner-to-me-encryption \
      --usage ENCRYPTION \
      --description "" \
      --certificate file://<PUBLIC_KEY_NAME>.pem
  ```

### Alternative: Import certificates with a certificate chain

If your partner's certificate was issued by a Certificate Authority, you must the provide its certificate chain. Upload your partner's signing and encryption certificates along with the appropriate signing chain bundle using the `--certificate-chain` option.

    ```shell
    stedi as2 import-certificate --name <NAME> \
        --description <DESCRIPTION> \
        --usage <USAGE_TYPE> \
        --private-key file://<PRIVATE_KEY_NAME>.pem \
        --certificate file://<PUBLIC_KEY_NAME>.pem \
        --certificate-chain file://<CHAIN>.pem
    ```

## Troubleshooting import errors

- You cannot update the cryptographic data of previously imported certificates. If a certificate import operation results in an `ERROR` status, delete the certificate and import it again with updated input.
- If you receive an error that says `Invalid request provided: Provided certificate is not self-signed and no chain was provided.`, this means that the certificate provided is not self-signed, so you'll need to [provide the certificate chain](https://www.stedi.com/docs/legacy/as2#alternative-import-certificates-with-a-certificate-chain) when importing the certificate.
- If you import a certificate that does not meet [the minimum requirements](https://www.stedi.com/docs/legacy/as2/limits#technical-specifications), you will receive an error that says `Certificate does not match required key length.`

# Profiles

Profiles combine a signing certificate and encryption certificate with an AS2 ID, which is an ID required by the AS2 protocol in order to help both parties identify messages. Your partner will provide you with their AS2 ID and you will need to provide your AS2 ID to your trading partner. You can choose a single arbitrary AS2 ID to use across all of your trading partners, such as `STEDIAS2`.

You need to create a local profile for you and a partner profile for your trading partner.

### Create local profile

1. Creating a profile requires certificate IDs, not certificate names, so the first step is to retrieve your certificate IDs.

   ```shell
   stedi as2 list-certificates
   stedi as2 describe-certificate --name <CERTIFICATE_NAME>
   ```

1. Create a local profile by supplying your AS2 ID and referencing your signing and encryption certificates.

   ```shell
   stedi as2 create-profile \
       --name <PROFILE_NAME> \
       --profile-type LOCAL \
       --as2-id <YOUR_AS2_ID> \
       --certificate-ids <SIGNING_CERT_ID>,<ENCRYPTION_CERT_ID>
   ```

### Create partner profile

1. Create a partner profile with your partner's AS2 ID (which they should have sent you already) and the certificate IDs for your trading partner's public signing and encryption keys.

   ```shell
   stedi as2 create-profile \
       --name <PROFILE_NAME> \
       --profile-type PARTNER \
       --as2-id <PARTNER_AS2_ID> \
       --certificate-ids <SIGNING_CERT_ID>,<ENCRYPTION_CERT_ID>
   ```

# Sending messages

You need a connector to send messages to your trading partner. You don't need a server (or an agreement, which is part of a server's configuration) unless you also want to receive messages.

## Connector

The connector combines all the configuration required to send messages to trading partners:

- A local profile containing your certificates and your AS2 ID
- A partner profile containing your trading partner's certificates and their AS2 ID
- The URL of your trading partner’s AS2 server.

1. Creating a connector requires profile IDs, not profile names, so the first step is to retrieve the profile IDs for your local profile and the partner profile.

   ```shell
   stedi as2 list-profiles
   stedi as2 describe-profile --name <LOCAL_PROFILE>
   stedi as2 describe-profile --name <PARTNER_PROFILE>
   ```

1. Obtain the server URL from your trading partner.

   > **Note:** Stedi AS2 doesn't yet support the less-common implementation of AS2 over `https://`. URLs must start with `http://`.

1. Create the connector.

For `http://` URLs, you have to specify an encryption algorithm. Supported algorithms are: `AES128_CBC`, `AES192_CBC`, and `AES256_CBC`. Note that `3DES` is not supported for outbound transmissions, though it is supported for inbound transmissions for backward compatibility.

Most trading partners will support all of them, in which case we recommend using `AES256_CBC`. If your partner documentation requires a specific algorithm, use that one.

    ```shell
    stedi as2 create-connector \
        --name <CONNECTOR_NAME> \
        --url <PARTNER_URL> \
        --local-profile-id <LOCAL_PROFILE_ID> \
        --partner-profile-id <PARTNER_PROFILE_ID> \
        --encryption-algorithm <ENCRYPTION_ALGORITHM> \
        --mdn-response SYNC
    ```

If you want to compress outbound files, you can optionally create the connector with the `--compression ZLIB` flag. ZLIB is the only supported compression algorithm.
