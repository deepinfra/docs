---
title: OpenAI API
full_title: Use OpenAI API clients with LLaMas | ML Models | DeepInfra
description: Find information about OpenAI API clients with LLaMas from getting started, choosing models, running OpenAI chat.completion, integration, and more!
---

We offer OpenAI compatible API for all [LLM models](/models/text-generation) and
all [Embeddings models](/models/embeddings).

The APIs we support are:
- [chat completion](https://platform.openai.com/docs/guides/gpt/chat-completions-api) — both streaming and regular
- [completion](https://platform.openai.com/docs/guides/gpt/completions-api) — both streaming and regular
- [embeddings](https://platform.openai.com/docs/guides/embeddings) — supported for all embeddings models.

The endpoint for the OpenAI APIs is `https://api.deepinfra.com/v1/openai`.

You can do HTTP requests. You can also use the official Python and Node.js libraries.
In all cases streaming is also supported.

### Official libraries

For Python you should run

```bash
pip install openai
```

For JavaScript/Node.js you should run


```bash
npm install openai
```

### Chat Completions

The Chat Completions API is the easiest to use. You exchange messages and it just works.
You can change the model to another LLM and it will continue working.

```python
from openai import OpenAI

openai = OpenAI(
    api_key="$DEEPINFRA_TOKEN",
    base_url="https://api.deepinfra.com/v1/openai",
)

stream = True # or False

chat_completion = openai.chat.completions.create(
    model="meta-llama/Meta-Llama-3-8B-Instruct",
    messages=[{"role": "user", "content": "Hello"}],
    stream=stream,
)

if stream:
    for event in chat_completion:
        if event.choices[0].finish_reason:
            print(event.choices[0].finish_reason,
                  event.usage['prompt_tokens'],
                  event.usage['completion_tokens'])
        else:
            print(event.choices[0].delta.content)
else:
    print(chat_completion.choices[0].message.content)
    print(chat_completion.usage.prompt_tokens, chat_completion.usage.completion_tokens)
```

```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  apiKey: "$DEEPINFRA_TOKEN",
  baseURL: 'https://api.deepinfra.com/v1/openai',
});

const stream = false; // or true

async function main() {
  const completion = await openai.chat.completions.create({
    messages: [{ role: "user", content: "Hello" }],
    model: "meta-llama/Meta-Llama-3-8B-Instruct",
    stream: stream,
  });

  if (stream) {
    for await (const chunk of completion) {
      if (chunk.choices[0].finish_reason) {
        console.log(chunk.choices[0].finish_reason,
                    chunk.usage.prompt_tokens,
                    chunk.usage.completion_tokens);
      } else {
        console.log(chunk.choices[0].delta.content);
      }
    }
  } else {
    console.log(completion.choices[0].message.content);
    console.log(completion.usage.prompt_tokens, completion.usage.completion_tokens);
  }
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "meta-llama/Meta-Llama-3-8B-Instruct",
      "stream": true,
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```

You can see more complete examples at the documentation page of each model.

### Conversations with Chat Completions

To create a longer chat-like conversation you have to add each response message and
each of the user's messages to every request. This way the model will have the context and
will be able to provide better answers. You can tweak it even further by providing a system message.

```python
from openai import OpenAI

openai = OpenAI(
    api_key="$DEEPINFRA_TOKEN",
    base_url="https://api.deepinfra.com/v1/openai",
)

stream = True # or False

chat_completion = openai.chat.completions.create(
    model="meta-llama/Meta-Llama-3-8B-Instruct",
    messages=[
        {"role": "system", "content": "Respond like a michelin starred chef."},
        {"role": "user", "content": "Can you name at least two different techniques to cook lamb?"},
        {"role": "assistant", "content": "Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I'm more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic \"Sous Vide\" method. Next, we have the ancient art of \"Sous le Sable\". And finally, we have the more modern technique of \"Hot Smoking.\""},
        {"role": "user", "content": "Tell me more about the second method."},
    ],
    stream=stream,
)

if stream:
    for event in chat_completion:
        if event.choices[0].finish_reason:
            print(event.choices[0].finish_reason,
                  event.usage['prompt_tokens'],
                  event.usage['completion_tokens'])
        else:
            print(event.choices[0].delta.content)
else:
    print(chat_completion.choices[0].message.content)
    print(chat_completion.usage.prompt_tokens, chat_completion.usage.completion_tokens)
```

```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: 'https://api.deepinfra.com/v1/openai',
  apiKey: "$DEEPINFRA_TOKEN",
});

const stream = false; // or true

async function main() {
  const completion = await openai.chat.completions.create({
    messages: [
      {role: "system", content: "Respond like a michelin starred chef."},
      {role: "user", content: "Can you name at least two different techniques to cook lamb?"},
      {role: "assistant", content: "Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I'm more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic \"Sous Vide\" method. Next, we have the ancient art of \"Sous le Sable\". And finally, we have the more modern technique of \"Hot Smoking.\""},
      {role: "user", "content": "Tell me more about the second method."}
    ],
    model: "meta-llama/Meta-Llama-3-8B-Instruct",
    stream: stream,
  });

  if (stream) {
    for await (const chunk of completion) {
      if (chunk.choices[0].finish_reason) {
        console.log(chunk.choices[0].finish_reason,
                    chunk.usage.prompt_tokens,
                    chunk.usage.completion_tokens);
      } else {
        console.log(chunk.choices[0].delta.content);
      }
    }
  } else {
    console.log(completion.choices[0].message.content);
    console.log(completion.usage.prompt_tokens, completion.usage.completion_tokens);
  }
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "meta-llama/Meta-Llama-3-8B-Instruct",
      "stream": true,
      "messages": [
        {
            "role": "system",
            "content": "Respond like a michelin starred chef."
        },
        {
          "role": "user",
          "content": "Can you name at least two different techniques to cook lamb?"
        },
        {
          "role": "assistant",
          "content": "Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I'"'"'m more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic \"Sous Vide\" method. Next, we have the ancient art of \"Sous le Sable\". And finally, we have the more modern technique of \"Hot Smoking.\""
        },
        {
          "role": "user",
          "content": "Tell me more about the second method."
        }
      ]
    }'
```

The longer the conversation gets, the more time it takes the model to generate the response.
The number of messages that you can have in a conversation is limited by the context size of a model.
Larger models also usually take more time to respond and are more expensive.

### Completions

This is an advanced API. You should know how to format the input to make it work.
Different models might have a different input format. The example below is for
[meta-llama/Meta-Llama-3-8B-Instruct](/meta-llama/Meta-Llama-3-8B-Instruct).
You can see the model's input format in the API section on its page.

```python
from openai import OpenAI

openai = OpenAI(
    api_key="$DEEPINFRA_TOKEN",
    base_url="https://api.deepinfra.com/v1/openai",
)

stream = True # or False

completion = openai.completions.create(
    model='meta-llama/Meta-Llama-3-8B-Instruct',
    prompt='<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n',
    stop=['<|eot_id|>'],
    stream=stream,
)

if stream:
    for event in completion:
        if event.choices[0].finish_reason:
            print(event.choices[0].finish_reason,
                  event.usage.prompt_tokens,
                  event.usage.completion_tokens)
        else:
            print(event.choices[0].text)
else:
    print(completion.choices[0].text)
    print(completion.usage.prompt_tokens, completion.usage.completion_tokens)
```

```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: 'https://api.deepinfra.com/v1/openai',
  apiKey: "$DEEPINFRA_TOKEN",
});

stream = true // or false

async function main() {
  const completion = await openai.completions.create({
    "model": "meta-llama/Meta-Llama-3-8B-Instruct",
    "prompt": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
    "stream": stream,
    "stop": [
      "<|eot_id|>"
    ]
  });

  if (stream) {
    for await (const chunk of completion) {
      if (chunk.choices[0].finish_reason) {
          console.log(chunk.choices[0].finish_reason,
                      chunk.usage.prompt_tokens,
                      chunk.usage.completion_tokens);
      } else {
          console.log(chunk.choices[0].text);
      }
    }
  } else {
    console.log(completion.choices[0].text);
    console.log(completion.usage.prompt_tokens, completion.usage.completion_tokens);
  }
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/openai/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
     "model": "meta-llama/Meta-Llama-3-8B-Instruct",
     "prompt": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
     "stop": [
       "<|eot_id|>"
     ]
   }'
```

For every model you can check its input format in the API section on its page.

### Embeddings

DeepInfra supports the OpenAI embeddings API.
The following creates an embedding vector representing the input text

```python
from openai import OpenAI

openai = OpenAI(
    api_key="$DEEPINFRA_TOKEN",
    base_url="https://api.deepinfra.com/v1/openai",
)

input = "The food was delicious and the waiter...", # or an array ["hello", "world"]

embeddings = openai.embeddings.create(
  model="BAAI/bge-large-en-v1.5",
  input=input,
  encoding_format="float"
)

if isinstance(input, str):
    print(embeddings.data[0].embedding)
else:
    for i in range(len(input)):
        print(embeddings.data[i].embedding)

print(embeddings.usage.prompt_tokens)
```

```javascript
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: 'https://api.deepinfra.com/v1/openai',
  apiKey: "$DEEPINFRA_TOKEN",
});

const input = "The quick brown fox jumped over the lazy dog" // or an array ["hello", "world"]

async function main() {
  const embedding = await openai.embeddings.create({
    model: "BAAI/bge-large-en-v1.5",
    input: input,
    encoding_format: "float",
  });

  // check if input is a string or array
  if (typeof input === "string") {
    console.log(embedding.data[0].embedding);
  } else {
    console.log(embedding.data.map((data) => data.embedding));
  }

  console.log(embedding.usage.prompt_tokens);
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/openai/embeddings" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
    "input": "The food was delicious and the waiter...",
    "model": "BAAI/bge-large-en-v1.5",
    "encoding_format": "float"
  }'
```

### Image Generation

You can use the OpenAI compatible API to generate images. Here's an example using Python:

```python
import io
import base64
from PIL import Image
from openai import OpenAI

client = OpenAI(
    api_key="$DEEPINFRA_TOKEN",
    base_url="https://api.deepinfra.com/v1/openai"
)

if __name__ == "__main__":
    response = client.images.generate(
        prompt="A photo of an astronaut riding a horse on Mars.",
        size="1024x1024",
        quality="standard",
        n=1,
    )
    b64_json = response.data[0].b64_json
    image_bytes = base64.b64decode(b64_json)
    image = Image.open(io.BytesIO(image_bytes))
    image.save("output.png")
```

```javascript
import * as fs from 'fs';
import OpenAI from "openai";

const openai = new OpenAI({
  baseURL: 'https://api.deepinfra.com/v1/openai',
  apiKey: "$DEEPINFRA_TOKEN",
});

async function main() {
  const response = await openai.images.generate({
    prompt: "A photo of an astronaut riding a horse on Mars.",
    size: "1024x1024",
    n: 1,
  });

  const b64Json = response.data[0].b64_json;
  const imageBuffer = Buffer.from(b64Json, 'base64');
  fs.writeFileSync('output.png', imageBuffer);
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/openai/images/generations" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
    "prompt": "A photo of an astronaut riding a horse on Mars.",
    "size": "1024x1024",
    "n": 1
  }'
```


## Model parameter

Some models have more than one version available, you can infer against
a particular version by specifying `{"model": "MODEL_NAME:VERSION", ...}` format.

You could also infer against a `deploy_id`, by using `{"model":
"deploy_id:DEPLOY_ID", ...}`. This is especially useful for 
[Custom LLMs](/docs/advanced/custom_llms), you can infer before the deployment is
running (and before you have the model-name+version pair).

## Caveats

Please note that we might not be 100% compatible yet, let us know in discord or by email
if something you require is missing. Supported request attributes:

ChatCompletions and Completions:

- `model`, including specifying `version`/`deploy_id` support
- `messages` (roles `system`, `user`, `assistant`)
- `max_tokens`
- `stream`
- `temperature`
- `top_p`
- `stop`
- `n`
- `presence_penalty`
- `frequency_penalty`
- `response_format` (`{"type": "json"}` only, it will return default format when omitted)
- `tools`, `tool_choice`
- `echo`, `logprobs` -- only for (non chat) completions

`deploy_id` might not be immediately avaiable if the model is currently deploying

Embeddings:

- `model`
- `input`
- `encoding_format` -- `float` only

Images:

- `model` -- Defaults to FLUX Schnell
- `quality` and `style` -- only available for compatibility.
- `response_format` --  only `b64_json` supported for now.

You can see even more details on each model's page.
