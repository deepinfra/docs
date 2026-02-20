---
title: Using Multimodal models on DeepInfra
---

DeepInfra hosts multimodal models that support vision and language models combined. These models can take both images and text as input and provide text as output.

Currently, we host:
* [meta-llama/Llama-3.2-90B-Vision-Instruct](/meta-llama/Llama-3.2-90B-Vision-Instruct)
* [meta-llama/Llama-3.2-11B-Vision-Instruct](/meta-llama/Llama-3.2-11B-Vision-Instruct)
* [Qwen/QVQ-72B-Preview](/Qwen/QVQ-72B-Preview)


## Quick start

Let's consider this image:

![Example image](https://shared.deepinfra.com/models/llava-hf/llava-1.5-7b-hf/cover_image.ed4fba7a25b147e7fe6675e9f760585e11274e8ee72596e6412447260493cd4f-s600.webp "Example image")

If you ask `What’s in this image?` 

The model will answer something like this
```text
In this image, a large, colorful animal, possibly a llama, is standing alone in a barren, red and orange landscape, close to a large volcano. The setting appears to be an artistic painting, possibly inspired by South American culture or a fantasy world with volcanoes. The llama is situated at the center of the scene, drawing attention to the contrasting colors and the fiery backdrop of the volcano. The overall atmosphere of the image suggests a sense of danger and mystery amidst the volcanic landscape.
```

Images are passed to the model in two ways:
1. by passing link to the image (e.g. https://example.com/image1.jpg)
2. by passing base64 encoded image directly in the request

Here is an example of the request.
```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
    "model": "meta-llama/Llama-3.2-90B-Vision-Instruct",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "image_url",
            "image_url": {
              "url": "https://shared.deepinfra.com/models/llava-hf/llava-1.5-7b-hf/cover_image.ed4fba7a25b147e7fe6675e9f760585e11274e8ee72596e6412447260493cd4f-s600.webp"
            }
          },
          {
            "type": "text",
            "text": "What’s in this image?"
          }
        ]
      }
    ]
  }'
```

## Example of uploading base64 encoded image

Uploading images using base64 is convenient when you have images available locally. Here is an example for it:

```python
from openai import OpenAI
import base64
import requests

# Create an OpenAI client with your deepinfra token and endpoint
openai = OpenAI(
    api_key="<your-DeepInfra-API-token>",
    base_url="https://api.deepinfra.com/v1/openai",
)

image_url = "https://shared.deepinfra.com/models/llava-hf/llava-1.5-7b-hf/cover_image.ed4fba7a25b147e7fe6675e9f760585e11274e8ee72596e6412447260493cd4f-s600.webp"
base64_image = base64.b64encode(requests.get(image_url).content).decode("utf-8")

chat_completion = openai.chat.completions.create(
    model="meta-llama/Llama-3.2-90B-Vision-Instruct",
    messages=[
        {
            "role": "user",
            "content": [
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{base64_image}"
                    }
                },
                {
                    "type": "text",
                    "text": "What’s in this image?"
                }
            ]
        }
    ]
)

print(chat_completion.choices[0].message.content)
```

## Passing multiple images

API allows to pass multiple images too. 

```bash
curl "https://api.deepinfra.com/v1/openai/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
    "model": "meta-llama/Llama-3.2-90B-Vision-Instruct",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "image_url",
            "image_url": {
              "url": "https://shared.deepinfra.com/models/llava-hf/llava-1.5-7b-hf/cover_image.ed4fba7a25b147e7fe6675e9f760585e11274e8ee72596e6412447260493cd4f-s600.webp"
            }
          },
          {
            "type": "image_url",
            "image_url": {
              "url": "https://shared.deepinfra.com/models/meta-llama/Llama-2-7b-chat-hf/cover_image.10373e7a429dd725e0eb9e57cd20aeb815426c077217b27d9aedce37bd5c2173-s600.webp"
            }
          },
          {
            "type": "text",
            "text": "What’s in this image?"
          }
        ]
      }
    ]
  }'
```
## Calculating costs

Images are tokenized and passed to the model as input. The number of tokens consumed by an image is reported in the response under `"usage":{"prompt_tokens": <tokens-for-images-and-text>,...}`.

Different models work with different image resolutions. You can still pass images of different resolutions, the model will rescale them automatically. Read the documentation of the model to know the supported image resolutions.

## Limitations and Caveats

* Supported image types are: jpg, png, and webp.
* Images must be smaller than 20MB
* Currently, we don't support passing image fidelity with `detail` argument.