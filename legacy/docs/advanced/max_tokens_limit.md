---
title: Max Output Tokens Limit
full_title: Understanding Max Output Tokens Limit of DeepInfra | ML Models | DeepInfra
description: Find information about Max Output Tokens Limit of DeepInfra endpoints, and how to continue responses beyond the limit.

---

The DeepInfra API has a maximum output token limit of 16384 tokens per request. 
This limit is in place to ensure efficient processing and prevent excessive response sizes. 
However, we understand that some use cases may require longer responses. 
In this documentation, we will explain how to work within this limit and how to continue responses beyond the maximum token limit.

### Understanding Max Output Tokens Limit 
The max tokens limit is the maximum number of tokens that can be generated in a single response. 
Tokens are the basic units of text, such as words or characters, that are used to construct the response. 
The 16384 token limit is sufficient for most use cases, but if you need to generate longer responses, 
you can use a technique called "response continuation" to continue the response beyond the limit.

### Continuing Responses Beyond the Limit 
To continue a response beyond the max tokens limit, you can send a new request with the previous response as the input. 
This will allow the model to pick up where it left off and generate the next part of the response. 
Here's an example of how to do this using the curl command:

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $API_KEY" \
    -d '{
        "model": "deepseek-ai/DeepSeek-R1",
        "messages": [
            { "role": "user", "content": "Hello!" },
            { "role": "assistant", "content": "**\<think>**\n\n**\</think>**\n\nHello" }
        ],
        "max_tokens": 5
    }'
```

In this example, the previous response is passed as the content of the assistant message, and the max_tokens parameter is set to 5. The model will then generate the next 5 tokens of the response, which can be used as the input for the next request.

If you have any questions or concerns about the max tokens limit, please don't hesitate to contact us vai feedback@deepinfra.com. We're always here to help.

### Limitations of Response Continuations

The response continuations technique can't help with generating responses that exceed the total context size of the model. You'll get 400 error once you exceed the total context size of the model.