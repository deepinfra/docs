---
title: DeepInfra API
---

DeepInfra's API is more advanced but gives you access to every model we provide
unlike OpenAI which only works with LLMs and embeddings. You can also do
[Image Generation](#image-generation), [Speech Recognition](#speech-recognition),
[Object detection](#object-detection), [Token classification](#token-classification), [Fill mask](#fill-mask),
[Image classification](#image-classification), [Zero-shot image classification](#zero-shot-image-classification)
and [Text classification](#text-classification)

### JavaScript

JavaScript is a first class citizen at DeepInfra. You can install our official client
https://github.com/deepinfra/deepinfra-node with

```bash
npm install deepinfra
```

### HTTP/Curl

Don't want another dependency?

You prefer Go, C#, Java, PHP, Swift, Ruby, C++ or something exotic?

No problem. You can always use HTTP and have full access to all features by DeepInfra.


### Completions/Text Generation

[List of text generation models](/models/text-generation)

You should know how to format the input to make completions work.
Different models might have a different input format. The example below is for
[meta-llama/Meta-Llama-3-8B-Instruct](/meta-llama/Meta-Llama-3-8B-Instruct)

```javascript
import { TextGeneration } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL_URL = 'https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct';

async function main() {
  const client = new TextGeneration(MODEL_URL, DEEPINFRA_API_KEY);
  const res = await client.generate({
    "input": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
    "stop": [
      "<|eot_id|>"
    ]
  });

  console.log(res.results[0].generated_text);
  console.log(res.inference_status.tokens_input, res.inference_status.tokens_generated)
}

main();
```

```bash
curl "https://api.deepinfra.com/v1/inference/meta-llama/Meta-Llama-3-8B-Instruct" \
   -H "Content-Type: application/json" \
   -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
   -d '{
     "input": "<|begin_of_text|><|start_header_id|>user<|end_header_id|>\n\nHello!<|eot_id|><|start_header_id|>assistant<|end_header_id|>\n\n",
     "stop": [
       "<|eot_id|>"
     ],
     "stream": false
   }'
```

For every model you can check its input format in its API section.

### Embeddings

[List of embeddings models](/models/embeddings)

The following creates an embedding vector representing the input text

```javascript
import { Embeddings } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "BAAI/bge-large-en-v1.5";

const main = async () => {
  const client = new Embeddings(MODEL, DEEPINFRA_API_KEY);
  const body = {
    inputs: [
      "What is the capital of France?",
      "What is the capital of Germany?",
      "What is the capital of Italy?",
    ],
  };
  const output = await client.generate(body);
  console.log(output.embeddings[0]);
};

main();
```

```bash
curl -X POST \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN"  \
    -F 'inputs=["I like chocolate"]'  \
    'https://api.deepinfra.com/v1/inference/BAAI/bge-large-en-v1.5'
```

### Image Generation

[List of image generation models](/models/text-to-image)

```javascript
import { TextToImage } from "deepinfra";
import { createWriteStream } from "fs";
import { Readable } from "stream";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "stabilityai/stable-diffusion-2-1";

const main = async () => {
  const model = new TextToImage(MODEL, DEEPINFRA_API_KEY);
  const response = await model.generate({
    prompt: "a burger with a funny hat on the beach",
  });

  const result = await fetch(response.images[0]);

  if (result.ok && result.body) {
    let writer = createWriteStream("image.png");
    Readable.fromWeb(result.body).pipe(writer);
  }
};

main();
```

```bash
curl "https://api.deepinfra.com/v1/inference/stabilityai/stable-diffusion-2-1" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
        "prompt": "a burger with a funny hat on the beach"
      }'
```

### Speech Recognition

[List of speech recognition models](/models/automatic-speech-recognition)

Text to speed for a locally stored `audio.mp3` file

```javascript
import { AutomaticSpeechRecognition } from "deepinfra";
import path from "path";
import { fileURLToPath } from 'url';


const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "openai/whisper-large";

const main = async () => {
  const client = new AutomaticSpeechRecognition(MODEL, DEEPINFRA_API_KEY);

  const input = {
    audio: path.join(__dirname, "audio.mp3"),
  };
  const response = await client.generate(input);
  console.log(response.text);
};

main();
```

```bash
curl -X POST \
    -H "Authorization: bearer $DEEPINFRA_TOKEN"  \
    -F audio=@audio.mp3  \
    'https://api.deepinfra.com/v1/inference/openai/whisper-large'

```

### Object Detection

[List of object detection models](/models/object-detection)

Send an image for detection

```javascript
import { ObjectDetection } from "deepinfra";
import path from "path";
import { fileURLToPath } from 'url';


const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "hustvl/yolos-small";

const main = async () => {
  const model = new ObjectDetection(MODEL, DEEPINFRA_API_KEY);

  const input = {
    image: path.join(__dirname, "image.jpg"),
  };
  const response = await model.generate(input);

  for (const result of response.results) {
    console.log(result.label, result.score, result.box);
  }
};

main();
```

```bash
curl -X POST \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN"  \
    -F image=@image.jpg  \
    'https://api.deepinfra.com/v1/inference/hustvl/yolos-small'
```

### Token Classification

[List of token classification models](/models/token-classification)

```javascript
import { TokenClassification } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "Davlan/bert-base-multilingual-cased-ner-hrl";

const main = async () => {
  const model = new TokenClassification(MODEL, DEEPINFRA_API_KEY);

  const input = {
    input: "My name is John Doe and I live in San Francisco.",
  };
  const response = await model.generate(input);

  console.log(response.results);
};

main();
```

```bash
curl -X POST \
    -d '{"input": "My name is John Doe and I live in San Francisco."}'  \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN"  \
    -H 'Content-Type: application/json'  \
    'https://api.deepinfra.com/v1/inference/Davlan/bert-base-multilingual-cased-ner-hrl'

```

### Fill Mask

[List of fill mask models](/models/fill-mask)

```javascript
import { FillMask } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "bert-base-cased";

const main = async () => {
  const model = new FillMask(MODEL, DEEPINFRA_API_KEY);

  const body = {
    input: "I need my [MASK] right now!",
  };
  const response = await model.generate(body);

  console.log(response.results);
};

main();

```

```bash
curl -X POST \
    -d '{"input": "I need my [MASK] right now!"}'  \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN"  \
    -H 'Content-Type: application/json'  \
    'https://api.deepinfra.com/v1/inference/bert-base-cased'
```

### Image Classification

[List of image classification models](/models/image-classification)


```javascript
import { ImageClassification } from "deepinfra";
import path from "path";
import { fileURLToPath } from 'url';


const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "google/vit-base-patch16-224";

const main = async () => {
  const model = new ImageClassification(MODEL, DEEPINFRA_API_KEY);

  const input = {
    image: path.join(__dirname, "image.jpg"),
  };
  const response = await model.generate(input);

  console.log(response.results);
};

main();
```

```bash
curl -X POST \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
    -F image=@image.jpg  \
    'https://api.deepinfra.com/v1/inference/google/vit-base-patch16-224'
```

### Zero-Shot Image Classification

[List of zero-shot image classification models](/models/zero-shot-image-classification)

```javascript
import { ZeroShotImageClassification } from "deepinfra";
import path from "path";
import { fileURLToPath } from 'url';


const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "openai/clip-vit-base-patch32";

const main = async () => {
  const model = new ZeroShotImageClassification(MODEL, DEEPINFRA_API_KEY);

  const body = {
    image: path.join(__dirname, "image.jpg"),
    candidate_labels: ["dog", "cat", "car", "horse", "person"],
  };

  const response = await model.generate(body);
  console.log(response.results);
};

main();
```

```bash
curl -X POST \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
    -F image=@image.jpg  \
    -F 'candidate_labels=["dog", "cat", "car", "horse", "person"]'  \
    'https://api.deepinfra.com/v1/inference/openai/clip-vit-base-patch32'
```


### Text Classification

[List of text classification models](/models/text-classification)

```javascript
import { TextClassification } from "deepinfra";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";
const MODEL = "ProsusAI/finbert";

const main = async () => {
  const model = new TextClassification(MODEL, DEEPINFRA_API_KEY);

  const body = {
    input:
      "Nvidia announces new AI chips months after latest launch as market competition heats up",
  };

  const response = await model.generate(body);
  console.log(response.results);
};

main();
```

```bash
curl -X POST \
    -d '{"input": "Nvidia announces new AI chips months after latest launch as market competition heats up"}'  \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
    -H 'Content-Type: application/json'  \
    'https://api.deepinfra.com/v1/inference/ProsusAI/finbert'
```
