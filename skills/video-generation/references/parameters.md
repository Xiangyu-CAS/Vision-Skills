# Veo 3.1 parameters (Python config)

Use `types.GenerateVideosConfig(...)` with snake_case field names.

## Fields

- `prompt` (str): Main text prompt for video generation.
- `negative_prompt` (str): What to avoid in the output.
- `image` (types.Image): Input image for image-to-video. Must include `imageBytes` and `mimeType`.
- `last_frame` (types.Image): Last frame for interpolation (use with `image` as the first frame). Must include `imageBytes` and `mimeType`.
- `reference_images` (list): Reference images for appearance consistency. Use `types.VideoGenerationReferenceImage` with `reference_type="asset"` and `types.Image` instances.
- `video` (Video): Input video for extension. Must be a prior Veo output.
- `aspect_ratio` (str): "16:9" or "9:16".
- `resolution` (str): "720p", "1080p", or "4k".
- `duration_seconds` (int): 4, 6, or 8. Use 8 seconds when required (see constraints).
- `person_generation` (str): "allow_all" or "allow_adult". "allow_adult" only with image-to-video, interpolation, or reference images. "allow_all" only with text-to-video or video extension.
- `seed` (int): Random seed for reproducibility.
- `number_of_videos` (int): For Veo 3.1, only 1 is supported.

## Constraints

- 1080p or 4k requires `duration_seconds=8`.
- Reference images, interpolation (first/last frames), and video extension require `duration_seconds=8`.
- Video extension is limited to 720p input videos and requires a Veo-generated video as input.
- `person_generation` is only supported in certain regions; requests can fail if unsupported.
