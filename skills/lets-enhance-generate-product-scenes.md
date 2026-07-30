---
name: Generate product scenes, backgrounds and AI edits with the Claid API
description: >-
  Create AI backgrounds and scenes, apply natural-language edits, generate images from prompts, and
  produce AI fashion model shots with the Let's Enhance Claid API.
api: openapi/lets-enhance-claid-openapi.json
provider: Let's Enhance
base_url: https://api.claid.ai/v1/
scopes:
  - image_editing
  - image_generation
operations:
  - create_scene_api_v1_scene_create_post
  - image_generate_v1_image_generate_post
  - async_ai_edit_generation_api_v1_image_ai_edit_post
  - async_ai_edit_generation_api_status_v1_image_ai_edit__ai_edit_id__get
  - async_try_on_generation_api_v1_image_ai_fashion_models_post
  - async_try_on_generation_async_status_v1_image_ai_fashion_models__processing_request_id__get
generated: '2026-07-19'
method: generated
source: https://docs.claid.ai/ai-background-api/ai-background-i-o
---

# Generate product scenes, backgrounds and AI edits with the Claid API

This is the generative half of Claid — placing a product into a scene, restyling an image from a
written instruction, generating imagery from a prompt, and putting garments on AI models.

## Mind the scope split

These operations do **not** all sit under one permission:

- `create_scene_api_v1_scene_create_post` (`POST /v1/scene/create`) requires **`image_generation`**.
- The AI edit, AI fashion model and image generation operations require **`image_editing`**.

In the Claid dashboard both fall under the *Image editing* permission group, so a key granted that
group covers all of them. A key scoped only to *Storage* returns `403` for every operation here.

## AI background scenes

`create_scene_api_v1_scene_create_post` (`POST /v1/scene/create`) composes a product into a
generated scene. It is controlled by two option families documented separately:

- **object** — how the product is positioned within the frame.
- **scene** — the quality and speed of the generated background.

This is the workhorse for ecommerce and marketplace listing imagery.

## Natural-language editing

`async_ai_edit_generation_api_v1_image_ai_edit_post` (`POST /v1/image/ai-edit`) modifies an image
from a written instruction. It is **asynchronous**: the call returns a job, and you read the result
from `async_ai_edit_generation_api_status_v1_image_ai_edit__ai_edit_id__get`
(`GET /v1/image/ai-edit/{ai_edit_id}`).

Note the path parameter is `ai_edit_id`, not the generic `task_id` used by the image-editing
endpoints — the async families do not share an id namespace. Keep the id with the endpoint that
produced it.

## AI fashion models

`async_try_on_generation_api_v1_image_ai_fashion_models_post`
(`POST /v1/image/ai-fashion-models`) generates model shots for apparel. Poll
`async_try_on_generation_async_status_v1_image_ai_fashion_models__processing_request_id__get`
(`GET /v1/image/ai-fashion-models/{processing_request_id}`) — again, its own id parameter name.

## Prompt-driven generation

`image_generate_v1_image_generate_post` (`POST /v1/image/generate`) generates an image from a
prompt, with documented options trading quality against speed.

## Operating rules

Generative work is slower and more expensive than deterministic editing, which changes how you
should drive it:

- **Use webhooks, not polling.** Configure them at <https://claid.ai/account/webhook> and verify the
  `X-Claid-Hmac-SHA256` signature over the raw body. Generation jobs are long enough that polling
  wastes a meaningful slice of the 120/minute budget.
- **Never auto-retry a generation.** There is no idempotency key, results are non-deterministic, and
  a retry bills again and returns a *different* image. Retry only when the submission itself failed
  with `429` or `5xx`.
- **Treat `402` as terminal.** It means the account is out of API call credits; the next call fails
  identically.
- **Download results promptly** — output URLs are temporary. For volume work, write straight to your
  own bucket via a storage connector.

Errors use the standard Claid envelope (`error_code`, `error_type`, `error_message`,
`error_details`); `422` carries field-level reasons in `error_details`. Keep the `x-request-id`
header for support.
