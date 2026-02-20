---
title: LangChain
full_title: Use LangChain with DeepInfra for language models | ML Models | DeepInfra
description: Find information about using LangChain with DeepInfra for language models, and more!
---

LangChain is a framework for developing applications powered by language models. To learn more, visit the [LangChain website](https://python.langchain.com/).

We offer the following modules:
- [Chat adapter](https://python.langchain.com/docs/integrations/chat/deepinfra)
  for most of [our LLMs](/models/text-generation)
- [LLM adapter](https://python.langchain.com/docs/integrations/llms/deepinfra)
  for most of [our LLMs](/models/text-generation)
- [Embeddings
  adapter](https://python.langchain.com/docs/integrations/text_embedding/deepinfra)
  for all of [our Embeddings models](/models/embeddings)

# Install LangChain

```bash
pip install langchain
pip install langchain-community
```

# LLM Examples

The examples below show how to use LangChain with DeepInfra for language models. Make sure to get your API key from DeepInfra. You have to [Login](https://deepinfra.com/login?from=%2Fdash) and get your token.

Please set `os.environ["DEEPINFRA_API_TOKEN"]` with your token.

_Read comments in the code for better understanding._

```python
import os
from langchain_community.llms import DeepInfra
from langchain.prompts import PromptTemplate
from langchain.chains import LLMChain

# Make sure to get your API key from DeepInfra. You have to Login and get a new token.
os.environ["DEEPINFRA_API_TOKEN"] = '<your DeepInfra API token>'

# Create the DeepInfra instance. You can view a list of available parameters in the model page
llm = DeepInfra(model_id="meta-llama/Meta-Llama-3-8B-Instruct")
llm.model_kwargs = {
    "temperature": 0.7,
    "repetition_penalty": 1.2,
    "max_new_tokens": 250,
    "top_p": 0.9,
}


def example1():
    # run inference
    print(llm.invoke("Who let the dogs out?"))

def example2():
    # run streaming inference
    for chunk in llm.stream("Who let the dogs out?"):
        print(chunk)

def example3():
    # create a prompt template for Question and Answer
    template = """Question: {question}

    Answer: Let's think step by step."""
    prompt = PromptTemplate(template=template, input_variables=["question"])

    # initiate the chain
    llm_chain = prompt | llm

    # provide a question and run the LLMChain
    question = "Can penguins reach the North pole?"
    print(llm_chain.invoke(question))

# run examples
example1()
```

## Chat Examples

Ensure the `DEEPINFRA_API_KEY` env is set to your api key.

```python
import os

# or pass deepinfra_api_token parameter to the ChatDeepInfra constructor
os.environ["DEEPINFRA_API_TOKEN"] = DEEPINFRA_API_TOKEN

from langchain_community.chat_models import ChatDeepInfra
from langchain_core.messages import HumanMessage
from langchain.callbacks.streaming_stdout import StreamingStdOutCallbackHandler

messages = [
    HumanMessage(
        content="Translate this sentence from English to French. I love programming."
    )
]

def example_sync():
  chat = ChatDeepInfra(model="meta-llama/Meta-Llama-3-8B-Instruct")
  print(chat.invoke(messages))

async def example_async():
  chat = ChatDeepInfra(model="meta-llama/Meta-Llama-3-8B-Instruct")
  await chat.agenerate([messages])

def example_stream():
  chat = ChatDeepInfra(
      streaming=True,
      verbose=True,
      callbacks=[StreamingStdOutCallbackHandler()],
  )
  print(chat.invoke(messages))
```

## Embeddings

```python
import os

os.environ["DEEPINFRA_API_TOKEN"] = DEEPINFRA_API_TOKEN

from langchain_community.embeddings import DeepInfraEmbeddings

embeddings = DeepInfraEmbeddings(
    model_id="sentence-transformers/clip-ViT-B-32",
    query_instruction="",
    embed_instruction="",
)

docs = ["Dog is not a cat", "Beta is the second letter of Greek alphabet"]
document_result = embeddings.embed_documents(docs)
print(document_result)
```
