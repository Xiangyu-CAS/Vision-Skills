# Model versions, features, and limitations

## Model versions (Gemini API)

- `veo-3.1-generate-preview`: Veo 3.1 preview model.
- `veo-3.1-fast-generate-preview`: Veo 3.1 fast preview model (speed-optimized).

Both support text and image inputs and output video with audio.

## Model features (Veo 3.1)

- Output video includes native audio.
- Input modalities: text-to-video, image-to-video, video-to-video (extension). Only Veo 3.1 supports video extension.
- Resolution: 720p, 1080p (8s only), 4k (8s only).
- Frame rate: 24 fps.
- Video duration: 4s, 6s, 8s (8s required for 1080p/4k or reference images).
- Videos per request: 1.

## Video extension specifics

- Extends a video by 7 seconds per request, up to 20 extensions.
- Input video length limit: 141 seconds; output limit: 148 seconds.
- Video extension is limited to 720p input videos.

## Limitations

- Request latency can range from 11 seconds to 6 minutes.
- Regional limits apply to `person_generation` (EU/UK/CH/MENA).
- Generated videos are stored for 2 days; download within 2 days (derived videos re-reference the storage window).
- Videos are watermarked with SynthID.
- Audio errors: videos can be blocked due to safety filters or other audio-processing issues.
