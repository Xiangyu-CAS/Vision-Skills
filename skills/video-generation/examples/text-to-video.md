# Example: Text-to-video (dialogue)

```python
from google import genai
from google.genai import types

client = genai.Client()

prompt = (
    "A close up of two people staring at a cryptic drawing on a wall, torchlight flickering. "
    "A man murmurs, 'This must be it. That's the secret code.' The woman looks at him and "
    "whispering excitedly, 'What did you find?'"
)

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt=prompt,
    config=types.GenerateVideosConfig(
        resolution="720p",
        duration_seconds=4,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"dialogue_example_{i}.mp4")
```
