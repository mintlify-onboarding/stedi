---
title: Error handling
sidebarTitle: "Error handling"
slug: cloud/functions/error-handling
product: ""
description: ""
searchable: true
childPageOrder: []
---

An important aspect of deploying functions is understanding how Functions handles errors and reports error details. We distinguish between two types of failures regarding function invocation errors:

- Unhandled exceptions from within the function code. Function executions that do not complete within the specified timeout are treated like unhandled function code errors.
- Errors returned by the Stedi Functions API.

## Unhandled exceptions

A likely cause of function errors is that the top-level handler throws an unhandled exception to the runtime. Consider the following function, which always throws an error.

```js
export const handler = async (event) => {
  throw new Error("Unhandled exception!");
};
```

When you invoke this function using the SDK, you receive an error response with the following structure. Some portions of the response are omitted for brevity.

```json
{
  "$metadata": {
    "httpStatusCode": 200
  },
  "functionName": "always-error",
  "payload": {
    "errorType": "Error",
    "errorMessage": "Unhandled exception!",
    "trace": [
      "Error: Unhandled exception!",
      "    at Runtime.handler (file:///var/task/index.mjs:2:9)",
      "    at Runtime.handleOnceNonStreaming (file:///var/runtime/index.mjs:1086:29)"
    ]
  },
  "logTail": "...",
  "error": "Unhandled",
  "invocationId": "..."
}
```

The special `$metadata` key is present in all Stedi SDK responses and provides a deeper insight into the service call's status.

In this case, the HTTP response code is 200, which indicates success even though function execution failed. Functions indicates success because the Invoke operation itself was successful. The function runtime received the request payload, the function ran, and then Stedi returned a response.

However, because the function code itself failed to complete successfully, the `error` key has a value of `"Unhandled"` to signal that there was an error during processing. The `payload` key contains further details about the error, instead of what might have been the function's output had it returned normally.

## Timeouts

When a function cannot finish running inside the configured timeout window, the Functions runtime terminates the running code and returns an error to the caller. Functions are deployed with a default timeout of 3 seconds, but you can set the timeout to any value between 1 and 900 seconds.

Timeouts are treated as the same way as unhandled function errors. For example, the example response metadata below indicates that this was a successful API call, despite the function timing out.

```json
{
  "$metadata": {
    "httpStatusCode": 200
  },
  "functionName": "timeout",
  "payload": {
    "errorMessage": "[...] Task timed out after 3.01 seconds"
  },
  "logTail": "...",
  "error": "Unhandled",
  "invocationId": "..."
}
```

## Functions API errors

Occasionally, the Functions service may reject a call due to bad input, overload, or even an internal service error. In those cases, the `$metadata` key contains an HTTP status code and an error name that details the cause. For example, if you attempt to call a function name that does not correspond to an existing function, you receive an error with the following structure.

```json
{
  "name": "ResourceNotFoundException",
  "$fault": "client",
  "$metadata": {
    "httpStatusCode": 404,
    "requestId": "..."
  }
}
```

Other common invocation-level errors may include rejected input due to size exceeding the maximum possible input, a function that has reached its concurrent executions limit, or, on rare occasions, an internal service error. In the latter case, the function sets the `$fault` key to `"server"` to indicate that it is the service that is at fault.
