---
title: Inference
---

Simple, scalable and cost-effective inference API is the main feature of DeepInfra.
We package state-of-the-art models into a simple rest API that you can use to build
your applications.

There are multiple ways to access the API with different endpoints. You can choose the one
that suits you best.

### OpenAI APIs
For LLMs there is the convenient OpenAI Chat Completions API, and the legacy
OpenAI Completions API. Embedding models also support the OpenAI APIs.

These can be accessed at the following endpoint

```url
https://api.deepinfra.com/v1/openai
```

This endpoint works with HTTP/Curl requests as well as with the official OpenAI libraries for Python & Node.js.

You can [learn more here](/docs/openai_api)

### Inference Endpoints

Every model also has a dedicated inference endpoint. 

```url
https://api.deepinfra.com/v1/inference/{model_name}
```

for example, for `meta-llama/Meta-Llama-3-8B-Instruct` the endpoint is

```url
https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct
```

These endpoints can be accessed with REST requests as well as with the [official DeepInfra Node.js library](https://github.com/deepinfra/deepinfra-node)

However, bare in mind that for certain cases, like LLMs, this API is more advanced and harder to uses than the messaging OpenAI Chat Completions API.

### Streaming

All LLM models support streaming with all APIs and libraries, you just have to pass the `stream` option.
You can see many examples in the API section of every model.

### Authentication

DeepInfra requires an API token to access any of its APIs. You can find yours in the [dashboard](/dash/api_keys) 

To authenticate your requests, you need to pass your API token in the 
`Authorization` header with type `Bearer`.

```
Authorization: bearer $AUTH_TOKEN
```

or pass it as a parameter to the appropriate library.

### Content types
Our inference API supports `multipart/form-data` and `application/json` content types.
We strongly suggest to use the latter whenever possible.

#### multipart/form-data
Using `multipart/form-data` makes sense when you want to send binary data
such as media files. Using this content type requires less bandwidth and
is more efficient for large files.

#### application/json
Using `application/json` makes sense when you want to send text data.
You can also use this content type for binary data, using data urls. 
For example:

```json
{
  "image": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBD..."
}
```

### HTTP Status Codes
We use standard HTTP status codes to indicate the status of the request.

- `200` - OK. The request was successful.
- `4xx` - Bad Request. The request was invalid or cannot be served.
- `5xx` - Internal Server Error. Something went wrong on our side.

### Response Body
The response body is always a JSON object containing the model output.
It also contains metadata about the inference request like `request_id`, `cost`, `runtime_ms` (except for LLMs), `tokens_input`, `tokens_generated` (LLMs only).

Example response:
```json
{
  "request_id": "RfMWDr1NXCd7cnaegcm3A8q0",
  "inference_status": {
    "cost": 0.004639499820768833,
    "runtime_ms": 1285,
    "status": "succeeded"
  },
  "text": "Hello World"
}
```
