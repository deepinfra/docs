---
title: LlamaIndex
full_title: Use LlamaIndex with DeepInfra endpoints | ML Models | DeepInfra
description: Find information about using LlamaIndex with DeepInfra endpoints, integration, and more!
---

[LlamaIndex](https://www.llamaindex.ai) is a popular data framework for LLM applications.
And now it works with DeepInfra.

## Large Language Models (LLMs)

### Installation

First, install the necessary package:

```bash
pip install llama-index-llms-deepinfra
```

### Initialization

Set up the `DeepInfraLLM` class with your API key and desired parameters:

```python
from llama_index.llms.deepinfra import DeepInfraLLM
import asyncio

llm = DeepInfraLLM(
    model="mistralai/Mixtral-8x22B-Instruct-v0.1",  # Default model name
    api_key="$DEEPINFRA_TOKEN",  # Replace with your DeepInfra API key
    temperature=0.5,
    max_tokens=50,
    additional_kwargs={"top_p": 0.9},
)
```

### Synchronous Complete

Generate a text completion synchronously using the `complete` method:

```python
response = llm.complete("Hello World!")
print(response.text)
```

### Synchronous Stream Complete

Generate a streaming text completion synchronously using the `stream_complete` method:

```python
content = ""
for completion in llm.stream_complete("Once upon a time"):
    content += completion.delta
    print(completion.delta, end="")
```

### Synchronous Chat

Generate a chat response synchronously using the `chat` method:

```python
from llama_index.core.base.llms.types import ChatMessage

messages = [
    ChatMessage(role="user", content="Tell me a joke."),
]
chat_response = llm.chat(messages)
print(chat_response.message.content)
```

### Synchronous Stream Chat

Generate a streaming chat response synchronously using the `stream_chat` method:

```python
messages = [
    ChatMessage(role="system", content="You are a helpful assistant."),
    ChatMessage(role="user", content="Tell me a story."),
]
content = ""
for chat_response in llm.stream_chat(messages):
    content += chat_response.delta
    print(chat_response.delta, end="")
```

### Asynchronous Complete

Generate a text completion asynchronously using the `acomplete` method:

```python
async def async_complete():
    response = await llm.acomplete("Hello Async World!")
    print(response.text)

asyncio.run(async_complete())
```

### Asynchronous Stream Complete

Generate a streaming text completion asynchronously using the `astream_complete` method:

```python
async def async_stream_complete():
    content = ""
    response = await llm.astream_complete("Once upon an async time")
    async for completion in response:
        content += completion.delta
        print(completion.delta, end="")

asyncio.run(async_stream_complete())
```

### Asynchronous Chat

Generate a chat response asynchronously using the `achat` method:

```python
async def async_chat():
    messages = [
        ChatMessage(role="user", content="Tell me an async joke."),
    ]
    chat_response = await llm.achat(messages)
    print(chat_response.message.content)

asyncio.run(async_chat())
```

### Asynchronous Stream Chat

Generate a streaming chat response asynchronously using the `astream_chat` method:

```python
async def async_stream_chat():
    messages = [
        ChatMessage(role="system", content="You are a helpful assistant."),
        ChatMessage(role="user", content="Tell me an async story."),
    ]
    content = ""
    response = await llm.astream_chat(messages)
    async for chat_response in response:
        content += chat_response.delta
        print(chat_response.delta, end="")

asyncio.run(async_stream_chat())
```

## Embeddings

[LlamaIndex](https://www.llamaindex.ai) can also work with DeepInfra [embeddings models](/models/embeddings) to get embeddings for your text data.

### Installation

```bash
pip install llama-index llama-index-embeddings-deepinfra
```

### Initialization

```python
from dotenv import load_dotenv, find_dotenv
from llama_index.embeddings.deepinfra import DeepInfraEmbeddingModel

_ = load_dotenv(find_dotenv())

model = DeepInfraEmbeddingModel(
    model_id="BAAI/bge-large-en-v1.5",  # Use custom model ID
    api_token="YOUR_API_TOKEN",  # Optionally provide token here
    normalize=True,  # Optional normalization
    text_prefix="text: ",  # Optional text prefix
    query_prefix="query: ",  # Optional query prefix
)
```

### Synchronous Requests

#### Get Text Embedding

```python
response = model.get_text_embedding("hello world")
print(response)
```

#### Batch Requests

```python
texts = ["hello world", "goodbye world"]
response_batch = model.get_text_embedding_batch(texts)
print(response_batch)
```

#### Query Requests

```python
query_response = model.get_query_embedding("hello world")
print(query_response)
```

### Asynchronous Requests

#### Get Text Embedding

```python
async def main():
    text = "hello world"
    async_response = await model.aget_text_embedding(text)
    print(async_response)

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```
