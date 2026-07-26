---
name: Upload audio and create a lip-synced video
description: Reserve a hosted audio upload, PUT the audio bytes, then create an audio-to-video Puppetry job.
api: openapi/elbo-ai-inc-puppetry-openapi-original.json
operations: [createHostedAudioUploadUrl, createVideoFromAudio, getVideoJob]
---

# Audio-to-video

Authenticate with `Authorization: Bearer pk_live_...`.

## Steps

1. **Reserve upload** — `POST /api/v1/uploads/audio-url`
   (`createHostedAudioUploadUrl`) with `mime_type` and `content_length`. Set
   `require_video_readiness: true` so it fails before reserving quota when credits or
   slots are blocked. It returns signed `upload_url` and short-lived `read_url`.
2. **PUT the bytes** — upload the audio to `upload_url` with matching `Content-Type`
   and `Content-Length`. Max 50MB per file.
3. **Create the job** — `POST /api/v1/videos/audio` (`createVideoFromAudio`) with
   `image_url` and `audio_url` = the `read_url` from step 1. Send an `Idempotency-Key`.
4. **Poll** — `GET /api/v1/videos/{jobId}` (`getVideoJob`) until `completed`
   (`video_url`) or `failed`.

## Rules
- `413` if audio exceeds 50MB; `415` for an unsupported `mime_type`.
- Reuse the same `Idempotency-Key` on retries (see conventions/).
