---
title: Webhooks
---

Webhooks are an exclusive feature of the DeepInfra API. They don't work with the OpenAI API.

Webhooks deliver inference results and notify you about inference errors.

Using them is simple. You just supply the optional webhook param like in the following examples

Here is an example with text generation.

```javascript
import { TextGeneration } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL_URL = 'https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct';

async function main() {
  const client = new TextGeneration(MODEL_URL, DEEPINFRA_API_KEY);
  const res = await client.generate({
    "input": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
    "stop": [
      "<|eot_id|>"
    ],
    "webhook": "https://your-app.com/deepinfra-webhook"
  });

  console.log(res.inference_status.status); // queued
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct" \
   -H "Content-Type: application/json" \
   -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
   -d '{
     "input": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
     "stop": [
       "<|eot_id|>"
     ],
     "webhook": "https://your-app.com/deepinfra-webhook"
   }'
```

Here is another example with embeddings.

```javascript
import { Embeddings } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "BAAI/bge-large-en-v1.5";

const main = async () => {
  const client = new Embeddings(MODEL, DEEPINFRA_API_KEY);
  const body = {
    inputs: [
      "I like chocolate",
    ],
    webhook: "https://your-app.com/deepinfra-webhook",
  };
  const output = await client.generate(body);
  console.log(output.inference_status.status); // queued
};

main();
```
```bash
curl "https://api.deepinfra.com/v1/inference/BAAI/bge-large-en-v1.5" \
   -H "Content-Type: application/json" \
   -H "Authorization: Bearer 90MrXD9iUpfVTSubGjwd6x6I8gO7nzwW" \
   -d '{
     "inputs": ["I like chocolate"],
     "webhook": "https://your-app.com/deepinfra-webhook"
   }'
```

When you provide a webhook the API server will respond with a __queued__ status and will call the webhook with the actual result.
Delivered response will contain inference result, cost estimate and runtime and/or an error in a JSON body. It is the same JSON
response that you get in a regular inference calls.

```json
{
    "request_id": "R7X9fdlIaF5GlVisBAi5xR3E",
    "inference_status": {
        "status": "succeeded",
        "runtime_ms": 228,
        "cost": 0.0001140000022132881
    },
    "results": {...}
}
```

Errors will have the following format
```json
{
    "request_id": "RHNShFanUP5ExA8rzgyDWH88",
    "inference_status": {
        "status": "failed",
        "runtime_ms": 0,
        "cost": 0.0
    }
}
```

We will make a few attempts if your webhook endpoint returns 400+ status.
