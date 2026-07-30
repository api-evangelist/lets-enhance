---
name: Enhance a product image with the Claid API
description: >-
  Upscale, restore, colour-correct and resize a single product photo synchronously with the Let's
  Enhance Claid API, and read the result metadata correctly.
api: openapi/lets-enhance-claid-openapi.json
provider: Let's Enhance
base_url: https://api.claid.ai/v1/
scopes:
  - image_editing
operations:
  - image_edit_v1_image_edit_post
  - image_edit_upload_v1_image_edit_upload_post
generated: '2026-07-19'
method: generated
source: https://docs.claid.ai/quick-start
---

# Enhance a product image with the Claid API

Use this skill for a single image that is small enough to process inline. For many images, or for
work that may take a while, use the bulk skill instead — the synchronous endpoint is not the right
tool for batches.

## Authenticate

Every call carries a bearer API key minted in the Claid dashboard at
<https://claid.ai/account/api>:

```
Authorization: Bearer {CLAID_API_KEY}
```

The key must carry the `image_editing` permission scope. A key without it returns `403` with
`error_type: auth`. Never put the key in a query string; Claid requires HTTPS.

## Submit the edit

Call `image_edit_v1_image_edit_post` (`POST /v1/image/edit`). The body is a declarative pipeline,
not a sequence of calls — describe the input, the operations and the desired output, and Claid
composes them:

```json
{
  "input": "https://example.com/source.jpg",
  "operations": {
    "restorations": { "decompress": "auto" },
    "resizing":     { "width": 800, "height": 800, "fit": "crop" },
    "adjustments":  { "hdr": 60, "sharpness": 40 }
  },
  "output": { "format": { "type": "jpeg", "quality": 90 } }
}
```

`input` must be a URL Claid can reach — it is fetched server-side. If the image only exists locally,
use `image_edit_upload_v1_image_edit_upload_post` (`POST /v1/image/edit/upload`) and send the bytes
directly instead of a URL.

## Read the response

A success returns `200` with the payload wrapped in a top-level `data` object holding `input` and
`output` metadata — `ext`, `mime`, `format`, `width`, `height`, `mps` — plus `output.tmp_url`.

**`tmp_url` is temporary.** Download the asset promptly, or register a storage connector and have
Claid write results straight to your own bucket (see the storage-connectors skill).

## Handle failure

Errors are **not** RFC 9457 problem+json. The body is Claid's own envelope:

```json
{ "error_code": "111", "error_type": "auth", "error_message": "Authorization is required.", "error_details": {} }
```

Surface `error_message` to humans. On `422`, read `error_details` — it maps each bad field path to
its reasons. Act on status:

| Status | Meaning | Do this |
|---|---|---|
| `401` | Missing or invalid key | Fix the `Authorization` header |
| `402` | Out of API call credits | Stop and alert; retrying will not help |
| `403` | Key lacks the scope | Re-scope the key to `image_editing` |
| `422` | Validation failure | Correct the fields named in `error_details` |
| `429` | Rate limited | Back off (see below) |
| `500` | Transient | Retry once, then report with `x-request-id` |

## Respect the rate limit

The default budget is **120 requests per minute and 4 per second**. Every response carries
`RateLimit-Limit`, `RateLimit-Remaining` and `RateLimit-Reset`. On `429`, sleep for
`RateLimit-Reset` seconds rather than retrying immediately.

**There is no idempotency key.** Re-posting a request creates a *new* job and consumes *new*
credits. Do not blindly retry a request that may have succeeded — retry only on `429` and `5xx`,
and deduplicate on your own side.

## When something goes wrong

Capture the `x-request-id` response header (a UUID) and include it when contacting
<support@claid.ai>. It is the only handle Claid support can trace a request by.
