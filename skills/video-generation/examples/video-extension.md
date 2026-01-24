# Example: Video extension

```python
from google import genai
from google.genai import types

client = genai.Client()

# Generate an initial video
initial_op = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt="A butterfly flutters through a meadow, landing on a flower.",
    config=types.GenerateVideosConfig(
        resolution="720p",
        duration_seconds=8,
    ),
)

while not initial_op.done:
    initial_op = client.operations.get(initial_op)

initial_video = initial_op.response.generated_videos[0].video

# Extend the video
extend_op = client.models.generate_videos(
    model="veo-3.1-fast-generate-preview",
    prompt="Track the butterfly into the garden, keep the camera movement smooth.",
    config=types.GenerateVideosConfig(
        video=initial_video,
        resolution="720p",
        duration_seconds=8,
    ),
)

while not extend_op.done:
    extend_op = client.operations.get(extend_op)

for i, video in enumerate(extend_op.response.generated_videos):
    client.files.download(file=video.video)
    video.video.save(f"extended_{i}.mp4")
```
