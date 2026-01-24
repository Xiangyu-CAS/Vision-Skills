# Overview (Python SDK)

## Requirements

- A Gemini API key is required; check the environment or a local `./.env` file. The SDK reads `GEMINI_API_KEY` from the environment by default.
- Install the Python SDK (Python 3.9+):

```bash
pip install -q -U google-genai
```

## Base workflow (text-to-video)

```python
from google import genai
from google.genai import types

client = genai.Client()

prompt = "A cinematic close-up of a cup of coffee with steam swirling"

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt=prompt,
    config=types.GenerateVideosConfig(
        resolution="720p",
        duration_seconds=4,
    ),
)

# Poll until done
while not operation.done:
    operation = client.operations.get(operation)

# Download and save
for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"video_{i}.mp4")
```

## Base workflow (image-to-video)

```python
from google import genai
from google.genai import types

client = genai.Client()

# First load a source image (local file or URL)
import mimetypes
import requests

def load_image(path_or_url: str) -> types.Image:
    if path_or_url.startswith(("http://", "https://")):
        resp = requests.get(path_or_url, timeout=30)
        data = resp.content
        mime_type = resp.headers.get("Content-Type", "image/png")
    else:
        with open(path_or_url, "rb") as f:
            data = f.read()
        mime_type = mimetypes.guess_type(path_or_url)[0] or "image/png"
    return types.Image(imageBytes=data, mimeType=mime_type)

image = load_image("input.png")

operation = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    image=image,
    prompt="Panning wide shot of a calico kitten sleeping in the sunshine.",
    config=types.GenerateVideosConfig(
        resolution="720p",
        duration_seconds=4,
    ),
)

# Poll until done
while not operation.done:
    operation = client.operations.get(operation)

# Download and save
for i, video in enumerate(operation.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"image_to_video_{i}.mp4")
```

## Notes

- `generate_videos` returns a long-running operation; always poll until `done`.
- Use `duration_seconds=8` for 1080p/4k, reference images, interpolation, or video extension.
- See `references/parameters.md` for available config fields and constraints.
