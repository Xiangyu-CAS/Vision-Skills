# Example: First and last frames (interpolation)

```python
from google import genai
from google.genai import types
from io import BytesIO

client = genai.Client()

first_frame = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents=(
        "A woman stands in a field of tall grass, her long dark hair blowing in the wind. "
        "The grass sways gently, and she seems to be at peace."
    ),
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
).parts[0].as_image()

last_frame = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents=(
        "A woman walks along a cliff edge overlooking the sea. Her hair blows in the wind, "
        "and the sky is a stormy gray."
    ),
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
).parts[0].as_image()

def pil_to_types_image(pil_image) -> types.Image:
    buf = BytesIO()
    pil_image.save(buf, format="PNG")
    return types.Image(imageBytes=buf.getvalue(), mimeType="image/png")

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    image=pil_to_types_image(first_frame),
    prompt=(
        "A ghostly woman walks slowly through a field, then she walks along a cliff "
        "overlooking the ocean."
    ),
    config=types.GenerateVideosConfig(
        last_frame=pil_to_types_image(last_frame),
        resolution="720p",
        duration_seconds=8,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"interpolation_{i}.mp4")
```
