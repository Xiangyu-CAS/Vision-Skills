# Example: Image-to-video

```python
from google import genai
from google.genai import types
from io import BytesIO

client = genai.Client()

# First create a source image
image_response = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents="A cute baby monkey, wearing a tiny cowboy hat.",
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
)

image_pil = image_response.parts[0].as_image()
buf = BytesIO()
image_pil.save(buf, format="PNG")
image = types.Image(imageBytes=buf.getvalue(), mimeType="image/png")

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    image=image,
    prompt="Panning wide shot of a calico kitten sleeping in the sunshine",
    config=types.GenerateVideosConfig(
        resolution="720p",
        duration_seconds=4,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"image_to_video_{i}.mp4")
```
