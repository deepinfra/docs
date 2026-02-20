---
title: Getting Started
---

You don't need to install anything to do your first inference.
You only need [your access token](/dash/api_keys).

Go to the API section on any model's page. Grab one of the examples. If you are logged in your
access token will be prefilled for you.

You can try one of the examples from [meta-llama/Meta-Llama-3-8B-Instruct](/meta-llama/Meta-Llama-3-8B-Instruct/api)

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "meta-llama/Meta-Llama-3-8B-Instruct",
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```

and it will respond with something like

```bash
{
    "id": "chatcmpl-guMTxWgpFf",
    "object": "chat.completion",
    "created": 1694623155,
    "model": "meta-llama/Meta-Llama-3-8B-Instruct",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": " Hello! It's nice to meet you. Is there something I can help you with or would you like to chat for a bit?"
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 15,
        "completion_tokens": 16,
        "total_tokens": 31,
        "estimated_cost": 0.0000268
    }
}
```

This example uses the [OpenAI Chat Completions API](/docs/openai_api) which we strongly recommend
because it is the most convenient to use when dealing with LLMs. You can also use it with
the official JavaScript/Node.js and Python libraries and they will work out of the box.

If you want to dip your toes a little more in the AI world you can try the following example

```bash
curl "https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct" \
   -H "Content-Type: application/json" \
   -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
   -d '{
     "input": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
     "stop": [
       "<|eot_id|>"
     ]
   }'
```

It is using DeepInfra's API and it require advanced knowledge of how the model works, which in turn gives you more flexiblity.
You can read specifics about each model in its API section including stop words, streaming and more.

You will get a response similar to the previous example

```bash
{
    "request_id": "RWZDRhS5kdoM1XWwXLEshynO",
    "inference_status": {
        "status": "succeeded",
        "runtime_ms": 243,
        "cost": 0.0000436,
        "tokens_input": 12,
        "tokens_generated": 25
    },
    "results": [
        {
            "generated_text": "Hello! It's nice to meet you. Is there something I can help you with or would you like to chat for a bit?"
        }
    ],
    "num_tokens": 25,
    "num_input_tokens":12
}
```
