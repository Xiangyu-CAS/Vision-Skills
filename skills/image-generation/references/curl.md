# REST / curl

## Quickstart requirements

- A Gemini API key is required; first check the environment or a local `.env` file. If missing, set it in the environment as `GEMINI_API_KEY`.

```bash
export GEMINI_API_KEY="YOUR_API_KEY"
```

## Recommended curl timeout

Image generation can take 20–30s. To reduce retries, set a higher client timeout (e.g. 45–60s).
When running commands via tools, align the tool's overall timeout with `--max-time` (default 60s unless the user asks otherwise).

```bash
# Example: set a 60s total timeout for the request
curl --max-time 60 ...
```

## Models

- Default: `gemini-3-pro-image-preview` (higher fidelity, 1K/2K/4K output).
- Optional: `gemini-2.5-flash-image` (faster/cheaper; best with <=3 input images).

## Limits and notes

- `gemini-3-pro-image-preview` supports up to 14 reference images total, including up to 6 object images (high fidelity) and up to 5 human images for consistency.
- `gemini-3-pro-image-preview` supports 5 images with high fidelity and up to 14 images total. `gemini-2.5-flash-image` works best with up to 3 input images.
- Inline image data is intended for smaller files; keep total request size under 20 MB.
- Image generation does not support audio or video inputs.
- The model may not follow an exact requested number of outputs.
- Best results: generate the exact text first, then ask for an image with that text.
- All generated images include a SynthID watermark.

## Output modalities

The model defaults to returning both text and images. To return only images:

```json
"generationConfig": {"responseModalities": ["IMAGE"]}
```

## Aspect ratios and image size

- Default output: if no input image, a 1:1 square; if input image is provided, output often matches its size.
- Aspect ratios: "1:1", "2:3", "3:2", "3:4", "4:3", "4:5", "5:4", "9:16", "16:9", "21:9".
- `gemini-3-pro-image-preview` supports `imageSize` "1K", "2K", or "4K" (uppercase K required).

## Text-to-image (image-only)

```bash
curl --max-time 60 "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
    "contents": [{"parts": [{"text": "A matte product shot of a green hiking backpack"}]}],
    "generationConfig": {
      "responseModalities": ["IMAGE"],
      "imageConfig": {"aspectRatio": "4:5", "imageSize": "1K"}
    }
  }'
```

## Image edit (text + inline image)

```bash
IMG_B64=$(base64 -w 0 input.png)

curl --max-time 60 "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
    "contents": [{"parts": [
      {"text": "Make the sky pastel pink"},
      {"inlineData": {"mimeType": "image/png", "data": "'"$IMG_B64"'"}}
    ]}],
    "generationConfig": {"responseModalities": ["IMAGE"]}
  }'
```

## Multi-reference composition

```bash
IMG1=$(base64 -w 0 p1.png)
IMG2=$(base64 -w 0 p2.png)
IMG3=$(base64 -w 0 p3.png)

curl --max-time 60 "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
    "contents": [{"parts": [
      {"text": "Compose a single scene that keeps the people consistent."},
      {"inlineData": {"mimeType": "image/png", "data": "'"$IMG1"'"}},
      {"inlineData": {"mimeType": "image/png", "data": "'"$IMG2"'"}},
      {"inlineData": {"mimeType": "image/png", "data": "'"$IMG3"'"}}
    ]}],
    "generationConfig": {"responseModalities": ["IMAGE"]}
  }'
```

## Grounding with Google Search

Use Google Search when the prompt needs real-time facts. Note: image-based search results are not passed to the generation model and are excluded from the response.

```bash
curl --max-time 60 "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '{
    "contents": [{"parts": [{"text": "Generate a visual moodboard for today's top space news"}]}],
    "tools": [{"google_search": {}}],
    "generationConfig": {"responseModalities": ["IMAGE"], "imageConfig": {"aspectRatio": "16:9"}}
  }'
```

## Response handling

Images are returned in `candidates[0].content.parts[*].inlineData` (base64 bytes). Iterate parts and decode the `data` field to save images.
