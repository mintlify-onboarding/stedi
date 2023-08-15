---
title: Invoking functions
sidebarTitle: "Invoking"
slug: cloud/functions/invoking
product: ""
description: ""
searchable: true
childPageOrder: []
---

After you [create and deploy](/docs/cloud/functions/creating-and-deploying) a function, you can use the `invoke` command to invoke it synchronously or asynchronously. You can also create [event bindings](/docs/cloud/functions/event-bindings) that automatically invoke the function in response to [Stedi Core](/docs/core) events.

# Sync vs Async

There are two fundamental ways to invoke a function, _synchronously_ or _asynchronously_.

Synchronous invocations will wait for the execution to complete, and return the result of the function right away - similar to calling a remote API. If the function execution fails or takes too long to finish, you also get an indication of the error as part of the response. The synchronous mode is useful for callers that require an answer before they can proceed. Another common use is testing your functions. Functions that take a long time to finish may not be best suited to synchronous invocations, as this also causes the caller to have to wait for a response.

Async invocations work quite differently. Asynchronous requests are added to a queue of items to be processed by the function. Stedi Functions pulls requests from this queue and invokes the function - if there is a large backlog, multiple copies of the function will be started to process the queued work in parallel. Any response from the function that is not an error is considered a success and will cause the request payload to be removed from the input queue. The return value from such successful function invocations is discarded. By default, any errors will be retried for 3 attempts in total, before being moved to a dedicated error queue. You can configure the number of retries for async requests for each Stedi function you create.

<picture>
  <source srcSet="/images/docs/functions/async-function.png" />
  <img alt="Diagram of an async function" src="/images/docs/functions/async-function.png" />
</picture>

The benefit of async invocations is handling spikes in load. If you had a function that took a while to run (say it made several external API calls), and you synchronously invoked it 10k times in 5 minutes, some requests are likely to start failing. But if those were async invocations, they'd just be added to the queue and worked through over time.

# Invoking functions

You can invoke functions manually through the UI or CLI and automatically with Stedi Core event bindings, function schedulers, and the Functions SDK.

## UI

To manually invoke functions in the Functions UI:

1. Open the function and click **invoke**.
1. Select the invocation type and supply an arbitrary request payload if needed. This is mostly useful for one-off testing.

## Event Bindings

You can invoke your function automatically in response to Stedi Core events with an event binding. For example, you could run a function when Core successfully processes an EDI transaction from a specific trading partner. Invocations triggered from event bindings are always asynchronous.

Visit [Event bindings](/docs/cloud/functions/event-bindings) for more details.

## Schedulers

You can define a scheduler to automatically invoke functions. For example, you could schedule a function to periodically poll an external API for new inventory data. The Functions UI shows schedulers created through the UI and through the SDK on each function's overview page.

Function schedules are independent from function event bindings. Applying a schedule to a function will cause the function to run on the schedule you specify, regardless of events that trigger the event binding.

To configure a scheduler in the [Functions UI](https://www.stedi.com/app/functions):

1. Click the function you want to schedule to go to its overview page.
1. Click **Add schedule**.
1. Set the schedule.
   - Use the **Basic** tab to define a simple scheduler that runs your function after a specific number of minutes, hours, or days.
   - Use the **Advanced** tab to enter a custom `cron` expression.
1. Click **Create schedule**.

You can also set function schedules using the [Functions SDK](https://stedi-sdk-javascript.vercel.app/clients/functions/docs/index.html). You must use the SDK when you would like to add more than a single function schedule per function or if you want to specify a timezone, disable or enable the schedule, or set time boundaries.

The following example shows how to set up a functions schedule using the SDK.

```javascript
import { EventsClient, CreateFunctionScheduleCommand } from "@stedi/sdk-client-events"; // ES Modules import
const client = new EventsClient({
  token: { token: accountToken },
});
const input = {
  functionScheduleName: "STRING_VALUE", // required
  idempotencyKey: "STRING_VALUE",
  /** startTime and endTime serve as time boundaries in which your schedule will be active. For example,
   *  you can choose to implement a schedule that runs once a day,
   * but only between the 1st and 31st of March.
   */
  endTime: "STRING_VALUE",
  functionName: "STRING_VALUE", // required
  /**
   * JSON input that will be passed to the target when the schedule is invoked.
   */
  functionInput: "DOCUMENT_VALUE",
  scheduleExpression: "STRING_VALUE", // required
  /**
   * Ensure your function runs at a certain time in a certain timezone.
   * Accepted values are listed in the IANA Time Zone Database: https://www.iana.org/time-zones
   *
   */
  scheduleExpressionTimezone: "STRING_VALUE",
  /** startTime and endTime serve as time boundaries in which your schedule will be active. For example,
   *  you can choose to implement a schedule that runs once a day,
   * but only between the 1st and 31st of March.
   */
  startTime: "STRING_VALUE",
  /**
   * If you would like to keep your functions schedule but want to temporarily disable it, you can use the
   * `state` property. When you are ready to enable the schedule again, use `new
   * UpdateFunctionScheduleCommand` with the `state` property set to `ENABLED`.
   */
  state: "ENABLED" || "DISABLED",
};
const command = new CreateFunctionScheduleCommand(input);
const {
  functionScheduleName,
  resourceDetail: { status },
} = await client.send(command);
```

### Schedule expressions

The Functions SDK accepts both `rate` and `cron` expressions.

#### Rate expressions

Use `rate` expressions to invoke a function at regular intervals. Intervals can be set to run at a specific number of days, hours, or minutes. The following table shows example `rate` expressions.

| Expression       | Result                                 |
| ---------------- | -------------------------------------- |
| rate(1 day)      | Run the function once a day            |
| rate(36 hours)   | Run the function every 36 hours        |
| rate(15 minutes) | Run the function every fifteen minutes |

#### Cron expressions

Cron expressions are a powerful way to schedule your function at a specific time or a specific interval. For example, you could use `cron` expressions to invoke a function at 8:00 AM (UTC+0) every first day of the month. The following table shows example `cron` expressions.

| Expression               | Result                                                    |
| ------------------------ | --------------------------------------------------------- |
| cron(15 4 \* \* MON-FRI) | At 04:15 on every day-of-week from Monday through Friday. |
| cron(0 12 1 \* \*)       | At 12:00 on the first day of every month.                 |
| cron(1 8,20 1 \* \*)     | At 8:01 and 20:01 on the first day of every month.        |

**Restrictions**

- You cannot include both the Day-of-month and Day-of-week fields simultaneously. If you assign a value (or \*) to one of these fields, you must use a ? (question mark) for the other.
- Cron expressions that result in your function running faster than once per 1 minute are not supported.

## Functions SDK

We recommend invoking functions with the SDK when you need to integrate Stedi Functions into an existing JavaScript codebase. You can invoke synchronously or asynchronously with the SDK.

Install the Stedi Functions SDK. The following command specifies that `npm` should download a package from Stedi Functions versions `0.2.x` or later.

```shell
npm install @stedi/sdk-client-functions@^0.2 --save-dev
```

Create a new file called `invoke.js` with the following contents.

```javascript
import { FunctionsClient, InvokeFunctionCommand } from "@stedi/sdk-client-functions";

async function invoke() {
  // The client gives us access to Stedi Functions.
  const functionsClient = new FunctionsClient({
    region: "us",
    apiKey: process.env.STEDI_API_KEY, // Read API key from environment variable.
  });

  // Invoke the function.
  const requestPayload = {}; // The payload is passed into the function as the event parameter of our handler

  const invokeFunctionCommand = new InvokeFunctionCommand({
    functionName: "stedi-function",
    payload: {}, // The payload object will be converted to JSON and passed to our function
    invocationType: "Synchronous", // Optional, defaults to sync. "Synchronous" or "Asynchronous".
  });
  const invokeFunctionResult = await functionsClient.send(invokeFunctionCommand);

  console.info("Invocation result:", invokeFunctionResult.payload);

  // The result includes the first couple of lines of the function log. It’s base64-encoded, so we
  // need to decode it first. In this example the log is small enough that it’s included in full.
  console.info("\nFunction log output:\n");
  console.info(invokeFunctionResult.logTail);
}

invoke();
```

Execute the code using Node.js, which will invoke the function deployed in your Stedi account.

```sh
node invoke.js
```

[SDK Reference](/docs/sdk)

## CLI

You can invoke events on the command line with the Stedi CLI. You can only invoke synchronously with the CLI. For example, the following command invokes a function called `stedi-function`.

```sh
npx stedi functions invoke-function \
    --function-name stedi-function \
    --payload file://event.json
```

Stedi returns JSON output similar to the following example.

```json
{
  "functionName": "stedi-function",
  "payload": {
    "message": "Hello, world!"
  },
  "logTail": ["..."],
  "invocationId": "<RANDOM_REQUEST_ID>"
}
```

The output contains the following properties:

- `payload` key: Contains the output of the function
- `logTail` key: Contains the last 4KB of logs from the function execution
- `invocationId`: Contains a unique identifier for this invocation. You can use this identifier to find a complete trace of this invocation in the Function Logs UI.

[CLI Documentation](https://www.npmjs.com/package/@stedi/cli)

## HTTP Invoke Function API

You can also invoke Functions directly over HTTP. The [Stedi SDK](/docs/sdk) provides convenient support for encoding the response, adding the necessary headers, and retrying common transient errors. However, we recognize that it is not always possible to use it with the platform of your choice.

| Request attribute                | Value                                                                                    |
| -------------------------------- | ---------------------------------------------------------------------------------------- |
| Endpoint URL                     | `https://functions.cloud.us.stedi.com/2021-11-16/functions/{function_name}/invocations`  |
| Method                           | `POST`                                                                                   |
| Authentication                   | HTTP Basic: `Key [API Key]`                                                              |
| Body                             | Request payload JSON                                                                     |
| Header `"X-Amz-Invocation-Type"` | [Optional] Set to `Event` for asynchronous invocation; no response body will be returned |

If you have defined an environment variable called `STEDI_API_KEY` containing a valid Stedi API key, a function in your account called `stedi-function`, and a local file containing valid JSON called `request.json`, you can invoke the function from the command line using `curl` as follows:

```
curl \
  --request POST \
  --header "Authorization: Key ${STEDI_API_KEY}" \
  --data @request.json' \
  "https://functions.cloud.us.stedi.com/2021-11-16/functions/${FUNCTION_NAME}/invocations"
```

The response body will contain the function's output in base64-encoded form. You can decode the response body by sending the output to the `base64` utility as seen in the following example:

```
% curl \
  --request POST \
  --header "Authorization: Key ${STEDI_API_KEY}" \
  --data '{"message":"Hello, world!"}' \
  --silent \
  "https://functions.cloud.us.stedi.com/2021-11-16/functions/stedi-function/invocations"  | base64 -d

{"event":{"message":"Hello, world!"}}
```

### Additional response information

The HTTP Response will contain some additional information that may be useful for diagnostic purposes.

| Response attribute               | Value                                                                                                      |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Header `x-stedi-invocationlogs`  | The last 4KB of logs from the function execution, base64-encoded                                           |
| Header `x-stedi-invocationid`    | A unique identifier for this invocation that can be used to find the full trace in the Logs UI             |
| Header `x-stedi-invocationerror` | If present and set to `Unhandled`, indicates that the function code execution resulted in error or timeout |

### Errors

Sample unhandled error:

```json
{
  "errorType": "Error",
  "errorMessage": "Error: d8920684-d2c0-4268-b301-edb24a1c4163",
  "trace": [
    "Error: Error: d8920684-d2c0-4268-b301-edb24a1c4163",
    "    at exports.handler (/var/task/index.js:4:9)",
    "    at Runtime.handleOnceNonStreaming (file:///var/runtime/index.mjs:1086:29)"
  ]
}
```

Sample function timeout:

```json
{
  "errorMessage": "2023-07-11T20:49:13.880Z b1c7a119-4797-4333-92fe-fcad90e58d14 Task timed out after 3.01 seconds"
}
```
