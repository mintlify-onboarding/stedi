---
title: "Manage mapping definitions with the UI"
slug: cloud/mappings/manage-mappings/ui-guide
description: Create, update, export, and delete mappings in the Stedi UI.
sidebarTitle: "Use Mappings UI"
searchable: true
childPageOrder: []
---

[mappings-product]: https://stedi.com/app/mappings
[disconnect-a-guide]: #disconnect-a-guide
[pull-changes-from-a-guide]: #pull-changes-from-a-guide

This page describes how to manage mapping definitions through the Mappings UI in your Stedi account. You also can do most of these steps with the Mappings API.

- Visit [Use the Mappings API](/docs/cloud/mappings/manage-mappings/api-guide) for full details and examples.
- Visit the [Mappings API Reference](http://localhost:3001/docs/api) for a complete list of endpoints and error codes.

Before you begin, we recommend reviewing the [Mapping Definitions Overview](/docs/cloud/mappings/manage-mappings/mapping-definition).

## Create a mapping

You can [create a mapping based on a Stedi guide](/docs/core/guides/create-a-mapping-from-a-stedi-guide) or create a blank mapping in your Stedi account.

To create a blank mapping:

1. Go to your [Stedi account](https://www.stedi.com/app/mappings), open the **Products** menu, and click **Mappings**. The mappings dashboard shows a list of any existing mappings in your account.
1. Click **Create mapping**. A blank mapping opens in the Mappings builder. You must [define a source and target JSON schema](#define-source-and-target-json-schema) before you can save the mapping.

## Edit or delete a mapping

> **Warning:** Once you delete a mapping, you cannot recover its data.

Go to your [Stedi account](https://www.stedi.com/app/mappings), open the **Products** menu, and click **Mappings**. The mappings dashboard shows a list of any existing mappings in your account.

- To edit a mapping, click the mapping's name to go to the Mappings builder.
- To delete a mapping, click the ellipses (`...`) next to the mapping you want to delete, click **Delete**, and then confirm the deletion. The mapping is removed from your Stedi account.

## Define source and target JSON schema

You must define a source and a target JSON Schema. The source schema describes your incoming data. The target schema describes the shape of the Mappings API output.

For example, if you need to transform EDI-like JSON from [Stedi Core](/docs/core) into a custom format for your internal systems, then the EDI-like JSON is the source, and the payload your software understands is the target.

You can define the source and target JSON Schemas by doing one of the following:

- [Connect a stedi guide](#connect-a-stedi-guide) to your mapping. We recommend this approach when you are using mappings with EDI Translate to read or write EDI data according to partner-specific requirements.
- Add a [JSON example file](#add-a-json-example-file).
- Provide a [JSON Schema](#provide-a-json-schema) adhering to [version 2020-12](https://json-schema.org/specification.html) that describes the shape of your data.

### Connect a Stedi guide

You can generate the **Source** or **Target** schema from a [Stedi guide](/docs/core/guides). After you connect a guide, the mappings UI autogenerates the schema and sample JSON document with fields from the guide.

#### Requirements

You must have at least one Stedi guide in your account. You can manually [create a guide from PDF EDI specifications](/docs/core/guides/demo-video) or import a pre-made guide from the [Stedi Network](https://www.stedi.com/edi/network).

#### Connect a guide to the mapping

You can connect a guide to both the mapping’s **Source** and **Target** schema. To connect a guide:

1. Navigate to your mapping and edit either the **Source** or the **Target** document.
1. Go to the **Guide** tab to view a list of guides in your Stedi account.
1. Click **Connect** next to the guide you want to use.

Once you've connected your guide, mappings generates the schema and example document based on the guide. The **Schema** and **Example** tabs will become read-only. Refer to [Pull changes from a guide][pull-changes-from-a-guide] on updating the document data based on a guide. To change the schema or example, you must first disconnect the guide – refer to [Disconnect a guide][disconnect-a-guide].

#### Pull changes from a guide

Pulling the changes from a guide allows you to update the schema with the latest published changes made to a guide.

To pull changes from a guide:

1. Navigate to your mapping and edit the **Source** or the **Target** document.
1. Go to the **Guide** tab to view the status of the connected guide.
1. Click **Edit** for a document that connects with a guide.
1. Click **Pull changes**. For **Target** schemas, the mappings UI alerts you when guide changes affect existing expressions in the mapping.
1. If there are breaking changes, review them and decide whether you want to continue with the update.

#### Disconnect a guide

Disconnecting a guide preserves the current **Source** and **Target** schemas. Still, you will no longer be able to automatically update them based on the published changes to a guide. You can re-connect a guide at any time if needed.
To disconnect a guide from a mapping document:

1. Edit the **Source** or **Target** and go to the **Guide** tab.
1. Click **Disconnect** and **Confirm**.

### Add a JSON example file

When you add a JSON example file, the Mappings builder autogenerates the JSON schema based on the example structure.

If you edit the JSON example file, the Mappings builder validates the changes against the existing JSON Schema. If the JSON document does not conform to the JSON schema, you can either regenerate JSON Schema or update it manually.

> **Important:** JSON Schema regeneration might remove fields you defined in the JSON schema and did not add to the example file.

### Provide a JSON schema

When you add a JSON schema, the Mappings builder autogenerates an example JSON file based on the schema.

Your JSON schema must define a `default` property that contains an example of data that adheres to the schema. Refer to the JSON documentation for more about [the `default` keyword](https://json-schema.org/understanding-json-schema/reference/generic.html).

When you upload a JSON schema as either the source or target, Mappings builder uses the schema's `default` property to produce the mapping output. If the contents of the `default` property do not conform to the JSON Schema, you can manually amend the contents of the `default` property, amend the JSON Schema itself, or regenerate it automatically.

## Choose target keys

You can choose whether you want to include all or a subset of the incoming JSON fields in the output JSON file. By default, the Mappings builder uses all incoming target keys.

If you only need a subset of the fields that are in the sample select **Target keys**, to change which keys are included.

## Choose a mapping type

The mapping type specifies how the Mappings API generates the output field when the mapping expression doesn't produce a value.

You can select a mapping type from the dropdown menu in the **Output** section. You can choose between:

- Only mapped
- Merge with target
- Pass through

Refer to [Mapping Definition Overview](/docs/cloud/mappings/manage-mappings/mapping-definition#mapping-types) for full details about each type and examples of how Mappings generates outputs based on your selection.

## Write mapping expressions

You must map each target key from the source JSON data to a property in the target JSON data. For example, you might map a `product ID` property from the source JSON to a `product number` property in the target JSON.

You do not need to do a one-to-one mapping of incoming properties to outgoing properties. You can create advanced expressions to map text properties to numbers, perform calculations, and more.

You can also use `omitField` to [exclude entire objects](/docs/cloud/mappings/jsonata/common-mapping-expressions#conditionally-omitting-objects) or [exclude fields](/docs/cloud/mappings/jsonata/common-mapping-expressions#omitting-output-fields) from the output based on a condition. For example, you might only want your output to include the input field `totalPrice` when the number of products is larger than 0.

Visit [Common Mapping Expressions](/docs/cloud/mappings/jsonata) for details and examples for common mapping use cases.

### Handling floating-point arithmetic

> **Warning:** Using JSONata for floating point arithmetic, such as financial calculations can introduce rounding errors. These rounding errors can accumulate and produce incorrect results.

Visit our [JSONata Playground](https://stedi.link/UKXfsxe) for an example.

Instead of JSONata, we recommend representing monetary values as integers (e.g. cents) and performing calculations on those integers.

## Export and import mappings

You can export an existing mapping and import it into another Stedi account.

To export a mapping, navigate to the [Mappings dashboard][mappings-product], click the ellipses (`...`) for the Mapping you want to export, and click **Export**. A .zip archive of the mapping data is downloaded to your machine.

To import a mapping .zip archive into a Stedi account: 1. Sign into the account and go to [Mappings dashboard][mappings-product]. 1. Click **Import**. 1. Either click **Upload** to choose a .zip archive from your machine or drag and drop the .zip archive onto the page. 1. Choose a **Mapping name** and click **Proceed with import**. The new mapping is now available in the Stedi account.

## Duplicate and version mappings

You may want to duplicate a mapping to create an updated version or to use an existing mapping as a starting point for a new one.

Duplicated mappings have a unique ID from the original that you can use in [Mappings API](https://www.stedi.com/docs/api/mappings) calls. This approach lets you quickly roll back to the previous version if necessary.

To duplicate a mapping:

1. Go to your [Mappings dashboard][mappings-product], click the ellipses (`...`) next to the mapping you want to duplicate, and select **Duplicate**.
1. Choose a name for the duplicated mapping and click **Duplicate**. The duplicated mapping is now available in your Stedi account.
