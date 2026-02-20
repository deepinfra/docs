---
title: Deploying LoRA adapter model
full_title: Deploying LoRA adapter model | ML Models | DeepInfra
description: How to deploy LoRA adapter model on DeepInfra
---

### How to deploy LoRA adapter model
1. Navigate to the dashboard https://deepinfra.com/dash
2. Click on the 'New Deployment' button
3. Click on the 'LoRA Model' tab
4. Fill the form:
    - **LoRA model name**: model name used to reference the deployment
    - **Hugging Face Model Name**: Hugging Face model name
    - **Hugging Face Token**: (optional) Hugging Face token if the LoRA adapter model is private

### To use LoRA adapter model, you need
1. LoRA adapter model hosted on Hugging Face
2. Base model that supports LoRA adapter at DeepInfra (you can see the list of supported base models in upload lora form)
3. Hugging Face token if the LoRA adapter model is private
4. DeepInfra account, and DeepInfra API key

### Example flow:
Prerequisites:
1. askardeepinfra/llama-3.1-8B-rank-32-example-lora
2. The base model is meta-llama/Meta-Llama-3.1-8B-Instruct which is supported at DeepInfra
3. The LoRA adapter model is public, so no need for Hugging Face token
4. DeepInfra API key is generated from https://deepinfra.com/dash/api_keys page

Then I'm gonna deploy the model:
1. Navigate to the dashboard https://deepinfra.com/dash
2. Click on the 'New Deployment' button
3. Click on the 'LoRA Model' tab
4. Fill the form:
    - **LoRA model name**: asdf/lora-example
    - **Hugging Face Model Name**: askardeepinfra/llama-3.1-8B-rank-32-example-lora
5. Click on the 'Upload' button

Now the deployment should appear in https://deepinfra.com/dash/deployments page, with a name asdf/lora-example.
Initially the state is "Initializing", after a while it should become "Deploying" and then "Running". Once the state is "Running", you can use the model.

Navigate to https://deepinfra.com/asdf/lora-example where you can find all the information about the the model including:
1. Pricing
2. Precision
3. Demo page, where you can test the model
4. API reference, where you can find information how to inference the model using REST API

I'll leave example of inference with curl below:
```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_API_KEY" \
  -d '{
      "model": "asdf/lora-example",
      "messages": [
        {
          "role": "user",
          "content": "Hello!"
        }
      ]
    }'
```
