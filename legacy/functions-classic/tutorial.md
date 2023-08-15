---
title: Tutorial
slug: legacy/functions-classic/tutorial
product: functions-classic
description: ""
searchable: false
childPageOrder: []
---

In this tutorial, we will show you how to deploy your first Stedi Function using our API. You can find more detailed information in the [API docs](/docs/api/legacy/functions).

## Authentication

Before you can get started with the API, you need an API key. If you already have an API key for another Stedi product, you can use that here as well; you don’t need a separate key for Functions.

Once you have the key, include it in every call to the API as part of the Authorization header of the HTTP request.

```
Authorization: Key your-api-key
```

If you’d like more information about authentication, take a look at our [Authentication guide](/docs/accounts-and-billing/authentication).

## Basics

### Code template

Your code must include a function called `handler` that looks like the following.

```javascript
exports.handler = async function (event, context) {
  return {};
};
```

An `event` is a JSON-formatted document that you pass in when you invoke the function; it contains data for a function to process. `context` contains information about the execution environment that the function is running in.

When writing a function, you should always return a value, or you’ll receive an error. Other than that, the return value is up to you.

#### Create a Function using the UI

In the [web UI](/app/functions), you can create a function by clicking _Create function_, adding code to the editor, and clicking _Save_. You cannot include libraries or additional packages using this method, for that you can find instructions [here](#providing-code-as-a-zipped-package-via-api).

#### Create a Function using the API

You can also create functions via the API. The following example creates a new function called `example-function` and includes the JavaScript code as a string.

```http
POST https://functions.cloud.us.stedi.com/2021-11-16/functions HTTP/1.1
Content-Type: application/json
Authorization: Key your-api-key

{
  "functionName": "example-function",
  "code": "exports.handler = async function (event, context) {\n  console.log('This is exciting!');\n  return { greeting: 'Hello, world!' }\n};"
}
```

A major downside of both the UI and the API methods shown above is that you can’t include multiple files, such as external packages. You can remedy this by creating a deployment package — which is a zip file with multiple files that include your code and libraries—and passing that via API instead.

#### Providing code as a zipped package via API

With this method, you include the zip file as a base64-encoded string in the API request. There are a few steps involved to create the zip file for every deployment:

- Run 'npm build' to download all required node modules.
- Create a zip file with your code and modules.
- Convert the zip file to a base64-encoded string.
- Deploy the base64-encoded package by making a request to the Functions API.

We encourage to do this through our deployment scripts linked on the [Stedi Demos on GitHub](https://github.com/Stedi-Demos/simple-function). In the web-request tutorial, you can find instructions for how to use the build script to create or update functions.

### Invoke a Function using the UI

In the [Functions UI](/app/functions), you can invoke your function by clicking _Execute_. Do remember to save your function before executing it. If you uploaded a package using the API, the UI won’t show the code of your function. You can still invoke it, though.

When the function is done, you can view the invocation result, which includes the function return value and the logs.

<picture>
  <source srcSet="/images/docs/functions/tutorial/image-1.png" media="(prefers-color-scheme: dark)" />

  <img alt="Screenshot of Stedi Terminal homepage" src="/images/docs/functions/tutorial/image-2.png" />
</picture>

### Invoke a function using the Stedi Functions SDK

You can also invoke a function using the Stedi SDK for JavaScript, which can be installed using `npm install -g @stedi/sdk-client-functions@0.0.x`. Once the SDK is installed, you could invoke a function and see the log response returns.

```javascript
const { FunctionsClient, InvokeFunctionCommand } = require("@stedi/sdk-client-functions");

const apiKey = "<replace-me>"; // Your Stedi API key
const functionName = "example-function"; // Your Stedi Function's name

// Create a Functions client
const client = new FunctionsClient({
  region: "us",
  apiKey: apiKey
});

async function main() {
  const output = await client.send(
    new InvokeFunctionCommand({
      functionName: functionName,
      requestPayload: Buffer.from(JSON.stringify({ name: "Functions Customer" }), "utf-8")
    })
  );

  const response = Buffer.from(output.responsePayload!).toString("utf-8");
  console.log("Function response:\n\n" + response + "\n");

  // Optional: Up to 4kb worth of the function's log output is returned in the invoke function response
  const logs = Buffer.from(output.invocationLogs!, "base64").toString("utf-8");
  console.log("Function logs:\n\n" + logs);
}

main();
```

### Passing data into a function

When you invoke a function, you can pass data into it. This data is available in the function through the `event` object.

The following function expects you to pass in a name and will echo that name to the log, with a little embellishment.

```javascript
exports.handler = async function (event, context) {
  console.log(`Well hello, my dear ${event.name}.`);
  return {
    greeting: `Hello, ${event.name}!`,
  };
};
```

#### Passing data into a function using the UI

If you click on _Edit invocation payload_, you can type in the input you want to pass to the function.

The invocation payload will be saved in your browser’s local storage, so you won’t have to type it in each time.

<picture>
  <source srcSet="/images/docs/functions/tutorial/image-1.png" media="(prefers-color-scheme: dark)" />

  <img alt="Screenshot of Stedi Terminal homepage" src="/images/docs/functions/tutorial/image-2.png" />
</picture>

### Updating a function

Updating a function is similar to creating a function: you can specify code as a string in the `code` field or as a zipped package in the `package` field. The differences are that you specify the function name in the URL instead of the body and that you use PUT instead of POST.

```http
PUT https://functions.cloud.us.stedi.com/2021-11-16/functions/example-function/ HTTP/1.1
Content-Type: application/json
Authorization: Key your-api-key

{
  "code": "exports.handler = async function (event, context) {\n  console.log('This is familiar.');\n  return { greeting: 'Hello again.' }\n};"
}
```

### Using third-party libraries

You can use any third-party library you want by zipping it along with your own code. This does require you deploy your function using the API, because the Functions UI doesn’t yet support zipped packages.

### A JavaScript example

For example, the following code translates Markdown to HTML, but it requires the Marked library to do so.

```javascript
import { marked } from "marked";

exports.handler = async function (event, context) {
  return { html: marked.parse(event.markdown) };
};
```

You can install the Marked library locally by running npm.

```bash
npm install marked
```

You then create a package by zipping the `node_modules` directory along with your function code.

```bash
zip package -r index.js node_modules/
```

After you’ve base64-encoded the zipped package, you can use it to [create a new function](#create-a-function-using-the-ui) or [update an existing one](#updating-a-function). For example, the following curl command updates `example-function`. Just make sure you set the environment variable `STEDI_API_KEY` to your API key.

> Other than the Stedi API key, you shouldn’t store secrets in environment variables. If your function needs access to passwords, API keys, and the like, store them in [Stash](/docs/stash) and access Stash from your function. The Stedi API key is the only exception, because you need it to access Stash.

```bash
curl --request PUT https://functions.cloud.us.stedi.com/2021-11-16/functions/example-functions \
  --header "Content-Type: application/json" \
  --header "Authorization: Key ${STEDI_API_KEY}" \
  --data-raw "{
    \"package\": \"$(base64 -i package.zip)\"
  }"
```

When calling this function, you need to [pass in](#passing-data-into-a-function) some Markdown, which doesn’t look pretty when you’re using curl, but that doesn’t mean it’s hard.

```bash
curl --request POST https://functions.cloud.us.stedi.com/2021-11-16/functions/example-function/invocations \
  --header "Authorization: Key ${STEDI_API_KEY}" \
  --header "Accept: application/octet-stream" \
  --data-raw "{ \"markdown\": \"# Greetings\nHello, world\" }"
```

### Calling web APIs

A function has access to the wider Internet, meaning you can call any public web API. The following example calls Stedi’s [EDI Core API](/docs/edi-core) because we like to promote our own products, but the principle is the same for any web API.

```javascript
const axios = require("axios");

exports.handler = async function (event, context) {
  let response = await axios.post(
    "https://edi-core.stedi.com/2021-06-05/translate",
    {
      input_format: "edi",
      output_format: "jedi@2.0",
      input:
        "ISA*00*          *00*          *ZZ*STEDI          *ZZ*ACME           *210901*1234*U*00801*000000001*0*T*>~\nGS*PO*SENDERGS*007326879*20210901*1234*1*X*008020~\nST*850*000000001~\nBEG*24*SP*PO-00001**20210901~\nN1*2L*STEDI INC.~\nREF*K6*A composable platform for building flexible EDI systems~\nPER*SR**EA*team@stedi.com~\nPO1**1*2P*0.0075*PE*GE*EDI Core~\nPO1**1*C0*0.001*PE*GE*Mappings~\nCTT*2~\nSE*9*000000001~\nGE*1*1~\nIEA*1*000000001~",
    },
    {
      headers: {
        "Authorization": `Key ${event.apiKey}`,
        "Content-Type": "application/json",
      },
    },
  );
  return response.data;
};
```

This code uses the Axios library, so it needs to be included in the package. The EDI Core API needs an API key and this function expects you to [pass it in using the `event` object](#passing-data-into-a-function).

```http
POST https://functions.cloud.us.stedi.com/2021-11-16/functions/example-function/invocations HTTP/1.1

Content-Type: application/json
Authorization: Key your-api-key
Accept: application/octet-stream

{
  "apiKey": "your-api-key"
}
```
