---
title: JSON Mode
full_title: Use JSON response format with DeepInfra endpoints | ML Models | DeepInfra
description: Understand how to generate high quality JSON responses on DeepInfra endpoints
---

In addition to responding in text, the DeepInfra API has an option to request that responsesbe returned in JSON format. [To learn more, read our blog](/blog/json-mode).

We provide JSON mode both in our inference API as well as our OpenAI compatible API, supported by [a lot of our models](/models?q=json).

## Using JSON Mode

Activating a JSON response in any of deepinfra's text APIs, including `/v1/inference`, `/v1/openai/completions` and `/v1/openai/chat/completions` is performed in the same way: adding a parameter `response_format` and setting its value to `{"type": "json_object"}`

### Example

Let's go through some simple example of learning about scientific discoveries.

This is how you set up our endpoint
```python
import openai
import json

client = openai.OpenAI(
    base_url="https://api.deepinfra.com/v1/openai",
    api_key="<Your-DeepInfra-API-Key>",
)
```

Here is an example of using the openai chat API to invoke a model with JSON mode:
```
messages = [
    {
        "role": "user",
        "content": "Provide a JSON list of 3 famous scientific breakthroughs in the past century, all of the countries which contributed, and in what year."
    }
]

response = client.chat.completions.create(
    model="mistralai/Mistral-7B-Instruct-v0.1",
    messages=messages,
    response_format={"type":"json_object"},
    tool_choice="auto",
)
```

The resulting `response.choices[0].message.content` will contain a string with JSON:
```json
{
  "breakthroughs": [
    {
      "name": "Penicillin",
      "country": "UK",
      "year": 1928
    },
    {
      "name": "The Double Helix Structure of DNA",
      "country": "US",
      "year": 1953
    },
    {
      "name": "Artificial Heart",
      "country": "US",
      "year": 2008
    }
  ]
}
```

## Caveats and warnings

It is highly recommended to prompt the model to produce JSON. While this is not strictly necessary, failing to prompt the model to produce JSON can occasionally produce nonsensical responses as the model may misunderstand your intent. For example, a model unaware it is producing JSON may mismatch a quote, leading to stray `:` characters appearing in strings, which while still technically valid JSON, may degrade the quality of the response.

Currently, the API will not guarantee the resulting JSON object is complete at the end of a response.

For example, if the model stops due to `length`, the JSON object in the response will be improperly terminated, for example in the middle of a string or object.

### A note about JSON and model alignment and accuracy

As a big warning and caveat of JSON mode, 
JSON mode interferes with model's alignment, or "self-control". In particular, when forced to produce a JSON response, the model will be more likely to make up information rather than explain that it does not know, or it will be more likely to behave in ways that fall outside of its training, producing undesirable output rather than objecting.

Let's take a really simple prompt:
```py
messages = [
    {
        "role": "user",
        "content": "What is the weather in San Francisco?"
    }
]
response = client.chat.completions.create(
    model="mistralai/Mistral-7B-Instruct-v0.1",
    messages=messages,
    tool_choice="auto",
)
```

This prompt, using the default `"text"` `response_format` will give a reasonable canned response:
```
" I don't have real-time updates or location tracking capabilities, so I can't provide current weather information for San Francisco. Please check a reliable weather website or app for this information."
```

However, now let's add `response_format={"type": "json_object"}`.
The model now merrily produces a made-up weather forecast with no objection:
```json
{
  "location":"San Francisco",
  "weather":[
    {
      "timestamp":163856000,
      "description":"Mostly cloudy",
      "temperature":25,
      "feels_like":26.2,
      "humidity":80,
      "wind":{
        "speed":4.7,
        "degrees":0
      }
    }
  ]
}
```

Because this output format effectively overly constrains the model in such a way that it cannot produce alignment warnings, it instead responds with the most probable tokens, a wildly inaccurate guess of today's weather.
