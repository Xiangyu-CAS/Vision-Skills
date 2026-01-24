# Example: Resolution (4k)

```python
from google import genai
from google.genai import types

client = genai.Client()

prompt = "A drone flies over the Grand Canyon at sunset, 4k resolution."

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt=prompt,
    config=types.GenerateVideosConfig(
        resolution="4k",
        duration_seconds=8,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"4k_grand_canyon_{i}.mp4")
```
