# Example: Reference images

```python
from google import genai
from google.genai import types
from io import BytesIO

client = genai.Client()

dress_image = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents="A beautiful red dress in a clothing store.",
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
).parts[0].as_image()

glasses_image = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents="A very small and thin gold frame, in front of a blue wall.",
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
).parts[0].as_image()

woman_image = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents="A woman with shoulder length brown hair.",
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
).parts[0].as_image()

def pil_to_types_image(pil_image) -> types.Image:
    buf = BytesIO()
    pil_image.save(buf, format="PNG")
    return types.Image(imageBytes=buf.getvalue(), mimeType="image/png")

reference_images = [
    types.VideoGenerationReferenceImage(image=pil_to_types_image(dress_image), reference_type="asset"),
    types.VideoGenerationReferenceImage(image=pil_to_types_image(glasses_image), reference_type="asset"),
    types.VideoGenerationReferenceImage(image=pil_to_types_image(woman_image), reference_type="asset"),
]

prompt = (
    "A woman in a red dress, white sneakers and sunglasses walks down a street in a city "
    "with a sidewalk."
)

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt=prompt,
    config=types.GenerateVideosConfig(
        reference_images=reference_images,
        resolution="720p",
        duration_seconds=8,
    ),
)

while not operation.done:
    operation = client.operations.get(operation)

for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"reference_{i}.mp4")
```
