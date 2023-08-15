---
title: Sending compressed payloads
slug: legacy/edi-core/compression
product: ""
description: ""
searchable: false
childPageOrder: []
---

To send a `gzip` compressed payload to the EDI Core API, set the `Content-Type` header to the value of `application/gzip`. EDI Core only supports `gzip` compression.

Note that the [maximum document size limit][core-limits] **is applied after decompressing the content of the payload**. If the decompressed payload of a given request is larger than the maximum document size limit, the EDI Core API will reject the request.

The following [TypeScript][typescript] example shows how to send a `gzip` compressed payload to the `/translate` endpoint.

```ts
import * as fs from "fs";
import axios from "axios";
import { gzipSync } from "zlib";

const API_KEY = "YOUR_API_KEY";
const rootURL = "https://edi-core.stedi.com/2021-06-05/translate";
const headers = {
  "Authorization": `Key ${API_KEY}`,
  "content-type": "application/gzip",
  "accept-encoding": "gzip",
};
const edi = fs.readFileSync(process.argv[2], "utf-8");

async function makeRequest(): Promise<void> {
  const request = {
    input: edi,
    input_format: "edi",
    output_format: "jedi@2.0",
  };

  const response = await axios.request({
    method: "post",
    url: url,
    data: gzipSync(JSON.stringify(request)),
    headers: headers,
  });
  console.log(response.status);
  console.log(response.data);
}

async function main() {
  try {
    await makeRequest();
  } catch (e) {
    console.log(e);
  }
}

main();
```

[core-limits]: https://www.stedi.com/docs/edi-core/limits
[typescript]: https://www.typescriptlang.org/
