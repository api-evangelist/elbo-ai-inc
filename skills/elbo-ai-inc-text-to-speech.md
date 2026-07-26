---
name: Generate Puppetry TTS speech
description: List Puppetry voices and synthesize speech from text with a public puppetry-* voice.
api: openapi/elbo-ai-inc-puppetry-openapi-original.json
operations: [listPuppetryVoices, createPuppetrySpeech]
---

# Text to speech

Authenticate with `Authorization: Bearer pk_live_...`. The Voice API uses Puppetry
voices only (no third-party providers).

## Steps

1. **List voices** — `GET /api/v1/voices/puppetry` (`listPuppetryVoices`). Each voice
   returns `id`, `name`, `language`, `language_code`, `gender`, and `preview_url`.
2. **Synthesize** — `POST /api/v1/tts/puppetry` (`createPuppetrySpeech`) with
   `voice_id` (a public `puppetry-*` id), `text`, and optional `speed`.

## Rules
- Voice ids passed to TTS must be the public `puppetry-*` id from the voice list; a
  bad id returns `404`.
- Fair-use: 100k Puppetry Voice characters/month, 10 req/min (see conventions/).
- On `429`, back off with `Retry-After` / `retry_after_seconds`.
