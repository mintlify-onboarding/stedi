---
title: Creating and deploying Functions
sidebarTitle: "Creating and deploying"
slug: cloud/functions/creating-and-deploying
product: ""
description: ""
searchable: true
childPageOrder: []
---

You can create and deploy functions using Stedi CLI. We also have a range of pre-made templates available on [GitHub](https://github.com/Stedi-Demos/function-templates).

# Stedi CLI

### Install the CLI

Install the latest version of the [Stedi CLI](https://www.npmjs.com/package/@stedi/cli). Requires [Node.js](https://nodejs.org/) version 18.16.0 or later.

```console
npm install --global @stedi/cli@latest
```

If you've installed the CLI previously, be sure to update it to the latest version using the same command.

### Init a project

Initialize and name a project.

```console
stedi integrations init --path=my-stedi-functions
cd my-stedi-functions
npm install
```

After initialization, change directories to the newly created directory and install dependencies.

### Create a function

Create your first function. The `new-function` command generates a basic Stedi Function placeholder, along with a test to help you get started. The `--guided` flag prompts you to select which Stedi [Core Event](/docs/events/event-types) you want your function should consume.

```console
npm run new-function -- hello-world --guided
```

Verify the setup by running the test for your newly created function. Out of the box, the test is expected to fail. Generated functions use the AVA test runner for Node.js.

```console
npm run test
```

### Deploy

Use an existing Stedi API key or [create a new one](https://www.stedi.com/app/settings/api-keys), and update the provided `.env` file in your project root directory.

The CLI requires this key to deploy your functions. You can reuse an existing API key; you don’t need a separate key for Functions.

```bash
STEDI_API_KEY=<YOUR_STEDI_API_KEY>
```

Deploy the function to your Stedi account.

```console
npm run deploy
```

You can view your new function in the Functions UI and [run test invocations](/docs/cloud/functions/invoking). To redeploy changes, run the `npm run deploy` command again.

We also recommend checking your code into source control.

# Function handler body

The following example shows the default `handler.ts`.

```TypeScript
import { type BucketsFileCreatedEvent } from "@stedi/integrations-sdk";

export const handler = async (event: BucketsFileCreatedEvent) => {
  console.dir(event, { depth: null });
};

```

The `event` parameter contains the request JSON payload that is passed in during the function invocation. Functions can accept any data shape. When a function is bound to handle events from other Stedi products, the events have a predefined schema that tells you what fields you can expect.

The provided typescript types (e.g. `BucketsFileCreatedEvent`) describe what shape is expected for each event type when using a function with an [event-binding](/docs/cloud/functions/event-bindings). You don't need to use [TypeScript](https://www.typescriptlang.org/) to use Stedi Functions, but we recommend it.

The `handler` can also accept a second parameter called `context`, which contains information about the execution environment where the function is running. It's not usually used, so it's not included in the default template.

## Using third-party libraries

You can import libraries from `npm` to use within your functions. The following example code requires the Marked library to translate Markdown to HTML. Install the Marked library.

```console
npm install --save-dev marked
```

Import the Marked library into your template.

```javascript
import { marked } from "marked";

export const handler = async (event) => {
  return {
    html: marked.parse(event.markdown),
  };
};
```

Your project comes with [esbuild](https://esbuild.github.io/) preinstalled and configured to create a single file that includes all of the dependencies (bundling).

When you run `npm run test` or `npm run deploy`, the necessary library code from the local `node_modules` will be included in the output file `markdown/index.js`.
