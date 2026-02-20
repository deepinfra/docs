---
title: AI SDK
full_title: Use AI SDK by Vercel with DeepInfra for language models | ML Models | DeepInfra
description: Find information about using AI SDK by Vercel with DeepInfra for language models, and more!
---

The [AI SDK](https://sdk.vercel.ai/) by Vercel is the AI Toolkit for TypeScript and JavaScript from the creators of Next.js.
It is a free open-source library that gives you the tools you need to build AI-powered products.

What's even better is that it works with [LLM models by DeepInfra](/models/text-generation) out of the box. You can check [AI SDK docs](https://sdk.vercel.ai/providers/ai-sdk-providers/deepinfra#deepinfra-provider).

# Install AI SDK

```bash
npm install ai @ai-sdk/deepinfra
```

# LLM Examples

The examples below show how to use the AI SDK with DeepInfra and large language models. Make sure to get your API key from DeepInfra. You have to [Login](https://deepinfra.com/login?from=%2Fdash) and get your token.

## Text Generation

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateText } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const { text, usage, finishReason } = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  prompt: "Write a vegetarian lasagna recipe for 4 people.",
});

console.log(text);
console.log(usage);
console.log(finishReason);
```

You can improve the answers further by providing a system message

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateText } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const { text, usage, finishReason } = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  prompt: "Write a vegetarian lasagna recipe for 4 people.",
  system:
    "You are a professional writer. " +
    "You write simple, clear, and concise content.",
});

console.log(text);
console.log(usage);
console.log(finishReason);
```

## Streaming

Generating text is nice, but your users don't want to wait when large amount of text is generated.
For those use cases you can use streaming.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { streamText } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const result = streamText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  prompt: "Invent a new holiday and describe its traditions.",
  system:
    "You are a professional writer. You write simple, clear, and concise content.",
});

for await (const textPart of result.textStream) {
  console.log(textPart);
}

console.log(await result.usage);
console.log(await result.finishReason);
```

### Conversations

To create a longer chat-like conversation you have to add each response message and
each of the user's messages to every request. This way the model will have the context and
will be able to provide better answers. You can tweak it even further by providing a system message.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateText } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const { text, usage, finishReason } = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  messages: [
    { role: "system", content: "Respond like a michelin starred chef." },
    {
      role: "user",
      content: "Can you name at least two different techniques to cook lamb?",
    },
    {
      role: "assistant",
      content:
        'Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I\'m more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic "Sous Vide" method. Next, we have the ancient art of "Sous le Sable". And finally, we have the more modern technique of "Hot Smoking."',
    },
    { role: "user", content: "Tell me more about the second method." },
  ],
});

console.log(text);
console.log(usage);
console.log(finishReason);
```

### Conversations & Streaming

Of course a conversation response can also be streaming and it is very simple.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { streamText } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const result = streamText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  messages: [
    { role: "system", content: "Respond like a michelin starred chef." },
    {
      role: "user",
      content: "Can you name at least two different techniques to cook lamb?",
    },
    {
      role: "assistant",
      content:
        'Bonjour! Let me tell you, my friend, cooking lamb is an art form, and I\'m more than happy to share with you not two, but three of my favorite techniques to coax out the rich, unctuous flavors and tender textures of this majestic protein. First, we have the classic "Sous Vide" method. Next, we have the ancient art of "Sous le Sable". And finally, we have the more modern technique of "Hot Smoking."',
    },
    { role: "user", content: "Tell me more about the second method." },
  ],
});

for await (const textPart of result.textStream) {
  console.log(textPart);
}

console.log(await result.usage);
console.log(await result.finishReason);
```

## Generating structured data

Getting text, streaming or not, is amazing but when two systems work together a structured approach
is even better.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateObject } from "ai";
import { z } from "zod";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const { object, usage, finishReason } = await generateObject({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  schema: z.object({
    recipe: z.object({
      name: z.string(),
      ingredients: z.array(z.object({ name: z.string(), amount: z.string() })),
      steps: z.array(z.string()),
    }),
  }),
  prompt: "Generate a lasagna recipe.",
});

console.log(object.recipe.name);
console.log(object.recipe.ingredients);
console.log(object.recipe.steps);
console.log(usage);
console.log(finishReason);
```

You can ask for more specific things like enums, too.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateObject } from "ai";
import { z } from "zod";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const { object, usage, finishReason } = await generateObject({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  output: "enum",
  enum: ["action", "comedy", "drama", "horror", "sci-fi"],
  prompt:
    "Classify the genre of this movie plot: " +
    '"A group of astronauts travel through a wormhole in search of a ' +
    'new habitable planet for humanity."',
});

console.log(object);
console.log(usage);
console.log(finishReason);
```

## Tool / Function calling

Tool calling allows models to call external functions provided by the user, and use the results to generate a comprehensive response to the user query. They are very powerful.

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateText, tool } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const result = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  tools: {
    weather: tool({
      description: "Get the weather in a location",
      parameters: z.object({
        location: z.string().describe("The location to get the weather for"),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  prompt: "What is the weather in San Francisco?",
  maxSteps: 2, // without it a text response is not generated, only the tool response
});

console.log(result.text);
console.log(result.usage);
console.log(result.finishReason);
```

## Conversations and tool calling

Let's see how tool calling works when you are having a conversation

```javascript
import { createDeepInfra } from "@ai-sdk/deepinfra";
import { generateText, tool } from "ai";

const deepinfra = createDeepInfra({
  apiKey: "$DEEPINFRA_TOKEN",
});

const messages = [
  { role: "user", content: "What is the weather in San Francisco?" },
];

const first_result = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  tools: {
    weather: tool({
      description: "Get the weather in a location",
      parameters: z.object({
        location: z.string().describe("The location to get the weather for"),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  messages: messages,
  maxSteps: 2, // without it a text response is not generated, only the tool response
});

console.log(first_result.text);

// Let's continue our conversation
messages.push(...result.response.messages);
messages.push({
  role: "user",
  content: "Is this normal temperature for the summer?",
});

const second_result = await generateText({
  model: deepinfra("meta-llama/Llama-3.3-70B-Instruct-Turbo"),
  tools: {
    weather: tool({
      description: "Get the weather in a location",
      parameters: z.object({
        location: z.string().describe("The location to get the weather for"),
      }),
      execute: async ({ location }) => ({
        location,
        temperature: 72 + Math.floor(Math.random() * 21) - 10,
      }),
    }),
  },
  messages: messages,
  maxSteps: 2,
});

console.log(second_result.text);
```
