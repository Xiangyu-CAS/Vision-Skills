# Python (google-genai)

## Quickstart requirements

- A Gemini API key is required; first check the environment or a local `./.env` file. The SDK reads `GEMINI_API_KEY` from the environment by default. If you still need an API key, ask for user Support.
- Install the Python SDK (Python 3.9+):

```bash
pip install -q -U google-genai
```

## Models

- Default: `gemini-3-pro-image-preview` (higher fidelity, 1K/2K/4K output).
- Optional: `gemini-2.5-flash-image` (faster/cheaper; best with <=3 input images).

## Limits and notes

- `gemini-3-pro-image-preview` supports up to 14 reference images total, including up to 6 object images (high fidelity) and up to 5 human images for consistency.
- `gemini-3-pro-image-preview` supports 5 images with high fidelity and up to 14 images total. `gemini-2.5-flash-image` works best with up to 3 input images.
- Image generation does not support audio or video inputs.
- The model may not follow an exact requested number of outputs.
- Best results: generate the exact text first, then ask for an image with that text.
- All generated images include a SynthID watermark.

## Output modalities

The model defaults to returning both text and images. To return only images:

```python
config=types.GenerateContentConfig(response_modalities=["IMAGE"])
```

## Aspect ratios and image size

- Default output: if no input image, a 1:1 square; if input image is provided, output often matches its size.
- Aspect ratios: "1:1", "2:3", "3:2", "3:4", "4:3", "4:5", "5:4", "9:16", "16:9", "21:9".
- `gemini-3-pro-image-preview` supports `image_size` "1K", "2K", or "4K" (uppercase K required).

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="A cinematic landscape photo at sunrise",
    config=types.GenerateContentConfig(
        response_modalities=["IMAGE"],
        image_config=types.ImageConfig(
            aspect_ratio="16:9",
            image_size="2K",
        ),
    ),
)

for part in response.parts:
    if image := part.as_image():
        image.save("landscape.png")
```

## Text-to-image

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="A minimal poster of a retro espresso machine, orange and teal",
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
)

for part in response.parts:
    if image := part.as_image():
        image.save("poster.png")
```

## Image edit (text + image)

```python
from google import genai
from google.genai import types
from PIL import Image

client = genai.Client()

prompt = "Replace the background with a warm sunrise, keep the subject"
image = Image.open("input.png")

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents=[prompt, image],
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
)

for part in response.parts:
    if image := part.as_image():
        image.save("edited.png")
```

## Multi-reference composition

```python
from google import genai
from google.genai import types
from PIL import Image

client = genai.Client()

prompt = "Compose a single scene that keeps the people consistent."
images = [Image.open(p) for p in ["p1.png", "p2.png", "p3.png"]]

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents=[prompt, *images],
    config=types.GenerateContentConfig(response_modalities=["IMAGE"]),
)

for part in response.parts:
    if image := part.as_image():
        image.save("composite.png")
```

## Grounding with Google Search

Use Google Search when the prompt needs real-time facts. Note: image-based search results are not passed to the generation model and are excluded from the response.

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="Visualize today's top space news as a poster",
    config=types.GenerateContentConfig(
        tools=[types.Tool(google_search=types.GoogleSearch())],
        response_modalities=["IMAGE"],
        image_config=types.ImageConfig(aspect_ratio="16:9"),
    ),
)

for part in response.parts:
    if image := part.as_image():
        image.save("space-news.png")

# grounding metadata:
# response.candidates[0].grounding_metadata
```
