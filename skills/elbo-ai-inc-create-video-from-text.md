---
name: Create a talking video from a photo and script
description: Turn a portrait image_url plus a text script into a Puppetry talking-head video, then poll to completion.
api: openapi/elbo-ai-inc-puppetry-openapi-original.json
operations: [getDeveloperApiUsage, listPuppetryVoices, createVideoFromText, getVideoJob]
---

# Create a talking video from text

Base URL `https://www.puppetry.com`. Authenticate every request with
`Authorization: Bearer pk_live_...` (a Studio API key from Studio → settings → API keys).

## Steps

1. **Check readiness** — `GET /api/v1/usage` (`getDeveloperApiUsage`). Confirm
   `video_generation.can_create` is true and `video_credits` remain. If blocked,
   back off using `retry_after_seconds`.
2. **Pick a voice** — `GET /api/v1/voices/puppetry` (`listPuppetryVoices`). Choose a
   public `puppetry-*` `id`; you can preview via `preview_url`.
3. **Create the job** — `POST /api/v1/videos/text` (`createVideoFromText`) with
   `image_url`, `text`, and `voice_id`. Send an `Idempotency-Key` header so retries
   are safe. A credit is charged only when the job is accepted (202); the response
   carries `id`/`jobId`, `status_url`, and a `Retry-After` hint.
4. **Poll** — `GET /api/v1/videos/{jobId}` (`getVideoJob`) until `status` is
   `completed` (then read `video_url`) or `failed`. Honor `Retry-After`; on `failed`
   check `retryable` / `retry_blocked_reason` before retrying.

## Rules
- Respect beta limits: 10 req/min, 2 concurrent jobs (see conventions/).
- On `402 insufficient_credits`, surface the returned `checkout_url` to the user.
- Errors follow the `{error, message}` envelope (see errors/elbo-ai-inc-problem-types.yml).
