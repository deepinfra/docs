---
title: Deploying Image LoRA adapter model
full_title: Deploying Image LoRA adapter model | ML Models | DeepInfra
description: How to deploy text to image generation LoRA adapter model on DeepInfra
---

### How to deploy Image LoRA adapter model
1. Navigate to the dashboard https://deepinfra.com/dash
2. Click on the 'New Deployment' button
3. Click on the 'LoRA text to image' tab
4. Fill the form:
    - **LoRA model name**: model name used to reference the deployment
    - **Base Model**: Select the base model for LoRA
    - **Civitai URL**: URL of the LoRA model from civitai.com

### Prerequisites
1. Public LoRA model from civitai.com
2. DeepInfra account


Deployment appears at https://deepinfra.com/dash/deployments with state "Initializing". Deployment time varies from 5s to 1min depending on LoRA size. Once "Running", model is ready.


### Inference Examples

Using direct API endpoint:
```bash
curl "https://api.deepinfra.com/yourname/yourmodel" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "prompt": "A cat in anime style",
      "lora_scale": 0.7
    }'
```

Using OpenAI compatible API endpoint:
```bash
curl "https://api.deepinfra.com/v1/openai/images/generations" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
      "model": "yourname/yourmodel",
      "prompt": "A cat in anime style",
    }'
```



### Notes
- Only public Civitai models are supported
- Base models support same parameters as their original versions
- LoRA scale parameter controls adaptation strength
