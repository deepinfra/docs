---
title: Function Calling
full_title: Use Function Calling with DeepInfra endpoints | ML Models | DeepInfra
description: Find information about using Function Calling with DeepInfra endpoints, integration, and more!
---

Function Calling allows models to call external functions provided by the user, and use the results to provide a comprehensive response to the user query. To learn more, read our [blog](/blog/function-calling-feature).

We provide OpenAI compatible API.

### Example

Let's go through some simple example of requesting a weather.

This is how you set up our endpoint

```python
import openai
import json

client = openai.OpenAI(
    base_url="https://api.deepinfra.com/v1/openai",
    api_key="<Your-DeepInfra-API-Key>",
)
```

```javascript
import OpenAI from "openai";

const client = new OpenAI({
    baseURL: 'https://api.deepinfra.com/v1/openai',
    apiKey: "<Your-DeepInfra-API-Key>",
});
```

This is the function that we will execute whenever the model asks us to do so
```python
# Example dummy function hard coded to return the same weather
# In production, this could be your backend API or an external API
def get_current_weather(location):
    """Get the current weather in a given location"""
    print("Calling get_current_weather client side.")
    if "tokyo" in location.lower():
        return json.dumps({
            "location": "Tokyo",
            "temperature": "75"
        })
    elif "san francisco" in location.lower():
        return json.dumps({
            "location": "San Francisco",
            "temperature": "60"
        })
    elif "paris" in location.lower():
        return json.dumps({
            "location": "Paris",
            "temperature": "70"
        })
    else:
        return json.dumps({"location": location, "temperature": "unknown"})
```

```javascript
// Example dummy function hard coded to return the same weather
// In production, this could be your backend API or an external API

// Get the current weather in a given location
async function get_current_weather(location) {
  console.log("Calling get_current_weather client side.")
  
  if (location.toLowerCase().includes("tokyo")) {
    return JSON.stringify({
      "location": "Tokyo",
      "temperature": "75"
    });
  } else if (location.toLowerCase().includes("san francisco")) {
    return JSON.stringify({
      "location": "San Francisco",
      "temperature": "60"
    });
  } else if (location.toLowerCase().includes("paris")) {
    return JSON.stringify({
      "location": "Paris",
      "temperature": "70"
    });
  } else {
      return JSON.stringify({"location": location, "temperature": "unknown"});
  }
}
```

Let's now call our DeepInfra endpoint with tools and a user request

```python
# here is the definition of our function
tools = [{
    "type": "function",
    "function": {
        "name": "get_current_weather",
        "description": "Get the current weather in a given location",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": 
                        "The city and state, e.g. San Francisco, CA"
                }
            },
            "required": ["location"]
        },
    }
}]

# here is the user request
messages = [
    {
        "role": "user",
        "content": "What is the weather in San Francisco?"
    }
]

# let's send the request and print the response
response = client.chat.completions.create(
    model="meta-llama/Meta-Llama-3.1-70B-Instruct",
    messages=messages,
    tools=tools,
    tool_choice="auto",
)
tool_calls = response.choices[0].message.tool_calls
for tool_call in tool_calls:
    print(tool_call.model_dump())
```

```javascript
// here is the definition of our function
const tools = [{
  "type": "function",
  "function": {
    "name": "get_current_weather",
    "description": "Get the current weather in a given location",
    "parameters": {
      "type": "object",
      "properties": {
        "location": {
          "type": "string",
          "description": "The city and state, e.g. San Francisco, CA"
        }
      },
      "required": ["location"]
    },
  }
}]
    

async function main() {
  // here is the user request
  const messages = [
    {
      "role": "user",
      "content": "What is the weather in San Francisco?"
    }
  ];

  // let's send the request and print the response
  const response = await client.chat.completions.create({
    model: "meta-llama/Meta-Llama-3.1-70B-Instruct",
    messages: messages,
    tools: tools,
    tool_choice: "auto",
  });

  const tool_calls = response.choices[0].message.tool_calls

  for (const tool_call of tool_calls) {
    console.log(tool_call);
  }
}

main();
```

Output:

```
{'id': 'call_X0xYqdnoUonPJpQ6HEadxLHE', 'function': {'arguments': '{"location": "San Francisco"}', 'name': 'get_current_weather'}, 'type': 'function'}
```

Now let's respond back with a function call response and see the results
```python
# extend conversation with assistant's reply
messages.append(response.choices[0].message)

for tool_call in tool_calls:
  function_name = tool_call.function.name
  if function_name == "get_current_weather":
      function_args = json.loads(tool_call.function.arguments)
      function_response = get_current_weather(
          location=function_args.get("location")
      )

  # extend conversation with function response
  messages.append({
      "tool_call_id": tool_call.id,
      "role": "tool",
      "content": function_response,
  })  


# get a new response from the model where it can see the function responses
second_response = client.chat.completions.create(
  model="meta-llama/Meta-Llama-3.1-70B-Instruct",
  messages=messages,
  tools=tools,
  tool_choice="auto",
)  

print(second_response.choices[0].message.content)
```

```javascript
// extend conversation with assistant's reply
messages.push(response.choices[0].message)

for (const tool_call of tool_calls) {
  const function_name = tool_call.function.name

  if (function_name == "get_current_weather") {
    const function_args = JSON.parse(tool_call.function.arguments);
    const function_response = await get_current_weather(function_args.location);

    // extend conversation with function response
    messages.push({
      "tool_call_id": tool_call.id,
      "role": "tool",
      "content": function_response,
    })
  }
}

// get a new response from the model where it can see the function responses
const second_response = await client.chat.completions.create({
  model: "meta-llama/Meta-Llama-3.1-70B-Instruct",
  messages: messages,
  tools: tools,
  tool_choice: "auto",
})

console.log(second_response.choices[0].message.content)
```

Output:
```python
The current temperature in San Francisco, CA is 60 degrees.
```

### Tips on using function calling

Here are some tips to get the most out of function calling:
* Make sure the descriptions of the functions are well written, it will make models perform better.
* Make sure to use lower temperatures < 1.0, this ensures the model won't plug in some random stuff to the parameters
* Try not to use system messages
* Models function calling quality degrades with the number of functions supplied.
* Try to keep top_p and top_k values on the default

### Notes

There is additional usage for prompting when using function calling + your function definitions will also be counted toward usage.

Supported:
* single calls
* parallel calls (though quality might be lower, it's under active development)
* `tool_choice` with only `auto` or `none`
* streaming mode

Not supported:
* nested calls (not supported)
