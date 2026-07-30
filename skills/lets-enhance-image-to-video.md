---
name: Turn a still image into video with the Claid API
description: >-
  Submit an image-to-video generation job to the Let's Enhance Claid API and collect the rendered
  animation by polling or webhook.
api: openapi/lets-enhance-claid-openapi.json
provider: Let's Enhance
base_url: https://api.claid.ai/v1/
scopes:
  - video_generation
operations:
  - video_generate_api_v1_video_generate_post
  - video_generate_api_status_v1_video_generate__animation_id__get
generated: '2026-07-19'
method: generated
source: https://docs.claid.ai/image-to-video-api/async-api-reference
---

# Turn a still image into video with the Claid API

Claid animates a still into a short video. This is the slowest and most expensive family in the API,
so it is worth driving carefully.

## Scope

These operations require the **`video_generation`** scope. In the Claid dashboard it sits inside the
*Image editing* permission group. A key without it returns `403`.

## Submit the job

Call `video_generate_api_v1_video_generate_post` (`POST /v1/video/generate`) with the source image
and the generation options documented in the image-to-video options reference.

The endpoint is **asynchronous only** — there is no synchronous variant. The response is an
acknowledgement carrying the job id; persist it immediately.

## Collect the result

Call `video_generate_api_status_v1_video_generate__animation_id__get`
(`GET /v1/video/generate/{animation_id}`). The path parameter is `animation_id` — distinct from the
`task_id` of the image-editing endpoints and the `ai_edit_id` of AI edit. Do not cross ids between
families.

Status progresses `ACCEPTED` → `PROCESSING` → `DONE`. A `404` means no job exists for that id.

## Prefer webhooks

Video renders take long enough that polling is wasteful. Subscribe to **Success pipelines** and
**Failed pipelines** at <https://claid.ai/account/webhook>, set a shared secret, and verify the
`X-Claid-Hmac-SHA256` header — HMAC-SHA256 over the *raw* request body — before acting on any
delivery.

## Cost discipline

Video generation has no idempotency key and is non-deterministic. **Never retry a submission that
may have been accepted**: you will pay twice and get two different animations. Retry only when the
submission itself failed with `429` or `5xx`, and record the returned id before anything else.

`402` means the account is out of API call credits — stop the run rather than queueing more work.

Errors use the standard Claid envelope; keep the `x-request-id` response header for
<support@claid.ai>.
