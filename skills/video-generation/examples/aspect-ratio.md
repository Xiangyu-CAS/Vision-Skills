# Example: Aspect ratio (portrait)

```python
from google import genai
from google.genai import types

client = genai.Client()

prompt = (
    "A montage of pizza making: a chef tossing and flattening the floury dough, "
    "ladling rich red tomato sauce in a spiral, sprinkling mozzarella cheese and pepperoni, "
    "and a final shot of the bubbling golden-brown pizza, upbeat electronic music with a "
    "rhythmical beat is playing, high energy professional video."
)

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt=prompt,
    config=types.GenerateVideosConfig(
        aspect_ratio="9:16",
        resolution="720p",
        duration_seconds=4,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"pizza_making_{i}.mp4")
```
