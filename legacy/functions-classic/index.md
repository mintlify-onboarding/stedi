---
title: Functions Classic
slug: legacy/functions-classic
product: functions-classic
description: ""
searchable: false
lifecycle: legacy
childPageOrder:
  - functions-classic/tutorial
  - functions-classic/notifications
  - functions-classic/pricing
  - functions-classic/limits
  - functions-classic/tutorial
---

> **Version Note:** This page covers the Functions Classic product; for the current generation of Functions please visit the [latest version of the Functions documentation](/docs/functions).

_Functions_ is a compute service that lets you run code without provisioning or managing servers. With Functions, Stedi runs your code on high-availability compute infrastructure that automatically scales up and down according to your usage. You can run code for virtually any type of integration or other standalone backend service – all you need to do is supply your source code.

Stedi runs your function only when needed and scales automatically, from a few requests per day to up to 50 concurrently by default. You pay only for the compute time that you consume — there is no charge to create functions, and there is no charge when your code isn't running.

## How can Functions be used?

You can invoke functions using the Functions Software Developer Kit (SDK). Another way that functions can run is [in response to activity within a Stedi Bucket](/docs/functions-classic/notifications#stedi-buckets). Here are a few ideas about what you could build:

- automatically process EDI files received via [SFTP](/docs/api/sftp) by sending them to the [EDI Translate API](/docs/edi-translate).
- send translated payloads to external API endpoints.
- call the [EDI Translate API](/docs/edi-translate) to translate EDI to JSON, and then transform the output using the [Mappings API](/docs/mappings).
- or do something completely unrelated to your other Stedi workflows!

## Features

Here’s what you can do with Functions.

- Execute code in a hosted environment.
- [Run code in response to supported events from other Stedi products.](/docs/functions-classic/notifications)
- Use third-party libraries.
- Call out to any external web API.

You can create, update, and invoke functions with the [Functions API](/docs/api/legacy/functions) or through the [web UI](https://www.stedi.com/app/functions). The API is more flexible and gives you access to create/update functions using a zip archive. You can follow [the deployment tutorial](/docs/functions/creating-and-deploying) to learn more about that. The web UI gets you up and running quickly, but it doesn’t support third-party libraries and it makes it hard to keep your code in a version control system. We recommend you use the web UI to experiment with Functions, and the API for production-level usage.

## A real-world example

Let's say that you receive [EDI 850 purchase orders](https://www.stedi.com/edi/x12/transaction-set/850) from your trading partner. The trading partner delivers these files to you via Stedi SFTP and you need to transform it somehow into an XML document for your ERP.

Using Stedi Functions, you can convert the EDI file to JSON. Next, you can transform the JSON into the desired JSON schema and convert the document to XML. Finally, the XML document is posted to an external webhook of the ERP.

You could use variations of this pattern to convert documents between other file formats and extend the function further with additional features.

You can find instructions for how to deploy your first Stedi Function in the [GitHub repo](https://github.com/Stedi-Demos/simple-function). We would also encourage you to take a look at the other [Stedi Demos](https://github.com/Stedi-Demos).

## Supported languages

- JavaScript (or any other language that compiles to JavaScript, like TypeScript).

<!-- "Choosing a module system" should only be present in the V2 docs - let's remove this from V1 doc when we split -->

### Choosing a module system

> **Version Note:** You can only choose a module system for the functions labeled `New (v2)` in the Functions UI.

Functions support both CommonJS (CJS) and ECMAScript (ESM) modules. Stedi infers the module system from the file extension: .js for CJS and .mjs for ESM.

You cannot change the module system if your function package contains multiple files (usually the case if it's been bundled). Changing a function module system impacts how the function behaves, so ensure the function's code is compatible with the selected module system before updating it.

To edit the module system for a function, go to that function in the UI, click **Edit**, and then select a module system from the menu at the top of the editor.

Please [contact us](https://www.stedi.com/contact) to request support for addtional languages.

## Limitations

There are some restrictions you should keep in mind.

- The name of a function can be up to 64 characters long and can only contain characters that are valid in a URL.
- The maximum execution time of a function is 15 minutes. Your function is deployed with a 3-second timeout by default, which you can increase using the API.
- Functions that require packages and other assets can only be deployed as a single zip archive. The overall size of the zip file is limited to 3.25 MB, and zip files can only be uploaded via API. Our web UI only supports self-contained code consisting of a single source code file.
- When a function has not run for a while, or when a large burst of requests arrives, the initial invocation(s) of a function may take a few seconds longer than normal. After such a "cold start" the function will be "warm" and its latency will settle down to just the execution time of your own code.

## API documentation

For detailed descriptions please refer to the [API reference](/docs/api/legacy/functions).
