---
title: Data privacy during Inference
---

DeepInfra offers simple, scalable and cost-effective inference APIs. The goal of this document is to explain how we handle data during inference when you use the DeepInfra APIs. When we mention a third party model, this means when you access that model through the DeepInfra APIs.

### Data Privacy

When using DeepInfra inference APIs, you can be sure that your data is safe. We do not store on disk the data you submit to our APIs. We only store it in memory during the inference process. Once the inference is done the data is deleted from memory.

We also don't store the output of the inference process. Once the inference is done the output is sent back to you and then deleted from memory.

Exceptions to these rules are outputs of Image Generation models which are stored for easy access for a short period of time.

If you opt to use Google model, Google will store the output as outlined in their [Privacy Notice](https://cloud.google.com/terms/cloud-privacy-notice).

If you opt to use the Anthropic model, Anthropic will store the output as outlined in their [Trust Center](https://trust.anthropic.com/).


### Bulk Inference APIs

When using our bulk inference APIs, you can submit multiple requests in a single
API call. This is useful when you have a large number of requests to make.
In this case we need to store the data for longer period of time, and we might
store it on disk in encrypted form. Once the inference is done and the output
is returned to you, the data is deleted from disk and memory after
a short period of time.


### No Training

Except for when you use the Google or Anthropic models, we do not use data for training our models. We do not store it on disk or use it for any other purpose than the inference process.

When using the Google or Anthropic models, the data you submit is subject to the receiving company’s training policy.

### No Sharing

Except for when you use the Google or Anthropic models, we do not share the data you submit to our APIs with any third party.

When using the Google or Anthropic models, we are required to transfer the data you submit to the company’s endpoints to facilitate the request.



### Logs

We generally don't log the data you submit to our APIs. We only log the metadata that might be useful for debugging purposes, like the request ID, the cost of the inference, the sampling parameters. We reserve the right to look at and log a small portions of requests when necessary for debugging or security purposes.

When using the Google model, Google logs prompts and responses for a limited period of time, solely for the purpose of detecting violations of their [Prohibited Use Policy](https://policies.google.com/terms/generative-ai/use-policy).


Personal information and data you provide when using certain models through our API may be shared with the relevant API endpoints, as specified at the time of use.
