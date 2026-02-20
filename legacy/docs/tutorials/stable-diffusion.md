---
title: Running Stable Diffusion on DeepInfra
---

## Pick a model

We support variety of text-to-image models, including Stable Diffusion versions 1.4, 1.5 and 2.1 and many derivatives. 
Pick a model from [the list of text-to-image models](/models/text-to-image)

For example, we'll use `stability-ai/sdxl`.

```javascript
import { Sdxl } from "deepinfra";
import { createWriteStream } from "fs";
import { Readable } from "stream";

const DEEPINFRA_API_KEY = "$DEEPINFRA_TOKEN";

const main = async () => {
  const model = new Sdxl(DEEPINFRA_API_KEY);

  const response = await model.generate({
    input: {
      prompt: "a burger with a funny hat on the beach",
    },
  });

  const result = await fetch(response.output[0]);

  if (result.ok && result.body) {
    let writer = createWriteStream("image.png");
    Readable.fromWeb(result.body).pipe(writer);
  }
};

main();
```

```bash
curl "https://api.deepinfra.com/v1/inference/stability-ai/sdxl" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $DEEPINFRA_TOKEN" \
  -d '{
        "input": {
          "prompt": "a burger with a funny hat on the beach"
        }
      }'
```

## Advanced options

Check [stability-ai/sdxl](/stability-ai/sdxl) for more options
