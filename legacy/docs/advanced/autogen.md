---
title: AutoGen
full_title: Use AutoGen with DeepInfra endpoints | ML Models | DeepInfra
description: Find information about using AutoGen with DeepInfra endpoints, integration, and more!
---

AutoGen is a framework that enables the development of LLM applications using multiple agents that can converse with each other to solve tasks. To learn more, please visit the [AutoGen](https://github.com/microsoft/autogen).


#### AutoGen with DeepInfra endpoints

```bash
# install autogen
pip install pyautogen
````

Here is how you can configure autogen to use DeepInfra endpoints. 

The `base_url` is `https://api.deepinfra.com/v1/openai`.
You can use any model which is OpenAI compatible. For example, [meta-llama/Meta-Llama-3-70B-Instruct](/meta-llama/Meta-Llama-3-70B-Instruct) is a model that can be used to solve coding tasks.

```python
import autogen

config_list = [
    {
        "model": "meta-llama/Meta-Llama-3-70B-Instruct",
        "base_url": "https://api.deepinfra.com/v1/openai",
        "api_key": "<your DeepInfra API key here>"
    }
]

llm_config={"config_list": config_list, "seed": 42}

assistant = autogen.AssistantAgent("assistant", llm_config=llm_config)
user_proxy = autogen.UserProxyAgent("user_proxy", code_execution_config={"work_dir": "coding"})
user_proxy.initiate_chat(assistant, message="What time is it right now?")
```

In the example, two agents converse and solve the task. The assistant agent provides a python code snippet which then gets executed on your local machine. In AutoGen, code execution is triggered automatically by the UserProxyAgent when it detects an executable code block in a received message.

Here is the output of the above code:

````text
user_proxy (to assistant):

What time is it now?

--------------------------------------------------------------------------------
assistant (to user_proxy):

To get the current time, you can use the `datetime` module in Python. Here's an example code:
```python
import datetime

current_time = datetime.datetime.now()
print(current_time.strftime("%I:%M %p"))
```
This code will print the current time in a 12-hour format with the AM/PM designation. If you want to print the time in a 24-hour format, you can use the `%H:%M` format specifier instead of `%I:%M %p`.

You can save this code in a file with a `.py` extension and run it in a terminal or command prompt to see the current time.

Note: This code assumes that you have Python installed on your computer. If you don't have Python installed, you can download it from the official Python website.

--------------------------------------------------------------------------------
Provide feedback to assistant. Press enter to skip and use auto-reply, or type 'exit' to end the conversation: 

>>>>>>>> NO HUMAN INPUT RECEIVED.

>>>>>>>> USING AUTO REPLY...

>>>>>>>> EXECUTING CODE BLOCK 0 (inferred language is python)...
execute_code was called without specifying a value for use_docker. Since the python docker package is not available, code will be run natively. Note: this fallback behavior is subject to change
user_proxy (to assistant):

exitcode: 0 (execution succeeded)
Code output: 
02:20 PM


--------------------------------------------------------------------------------
assistant (to user_proxy):

It looks like the code you provided ran successfully and returned the current time in a 12-hour format with the AM/PM designation. Here's the output:

02:20 PM

If you have any other questions or need further assistance, feel free to ask!

--------------------------------------------------------------------------------
Provide feedback to assistant. Press enter to skip and use auto-reply, or type 'exit' to end the conversation: exit
````
