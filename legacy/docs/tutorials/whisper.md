---
title: Running Whisper using DeepInfra
---

## Speech recognition made easy

[Whisper](https://github.com/openai/whisper) is a Speech-To-Text model from OpenAI.

Given an audio file with voice data it produces human speech recognition text with per sentence timestamps.
There are different model sizes (small, base, large, etc.) and variants for English.

You can see all [speech recognition models](/models?type=automatic-speech-recognition) that we currenly provide.

By default, Whisper produces by sentence timestamp segmentation.
We also host [whisper-timestamped](/openai/whisper-timestamped-medium) which can provide by word timestamp segmentation.

Whisper is fully supported by our REST API and our Node.js client.

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
    audio: path.join(__dirname, "/home/user/audio.mp3"),
  };
  const response = await client.generate(input);
  console.log(response.text);
};

main();
```

```bash
curl -X POST \
    -H "Authorization: Bearer $DEEPINFRA_TOKEN"  \
    -F audio=@/home/user/audio.mp3  \
    'https://api.deepinfra.com/v1/inference/openai/whisper-large'
```



You can pass audio formats like mp3 and wav.

To see additional parameters and how to call this model checkout out its [documentation page](/openai/whisper-large)  
