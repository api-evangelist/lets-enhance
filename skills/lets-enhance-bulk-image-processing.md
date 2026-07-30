---
name: Process images in bulk with the Claid async and batch APIs
description: >-
  Submit asynchronous and batch image editing jobs to the Let's Enhance Claid API, poll for results
  by task id, and receive HMAC-signed completion webhooks instead of polling.
api: openapi/lets-enhance-claid-openapi.json
provider: Let's Enhance
base_url: https://api.claid.ai/v1/
scopes:
  - image_editing
operations:
  - async_image_edit_v1_image_edit_async_post
  - async_image_edit_result_v1_image_edit_async__task_id__get
  - process_batch_image_edit_v1_image_edit_batch_post
  - image_edit_batch_async_results_v1_image_edit_batch__task_id__get
generated: '2026-07-19'
method: generated
source: https://docs.claid.ai/image-editing-api/async-api-reference
---

# Process images in bulk with the Claid async and batch APIs

Anything beyond a single small image belongs here. The synchronous `/v1/image/edit` endpoint holds
the connection open for the whole pipeline and will fight the 4-requests-per-second limit; the async
and batch endpoints will not.

## Pick the right endpoint

- **One image, don't wait** — `async_image_edit_v1_image_edit_async_post`
  (`POST /v1/image/edit/async`).
- **Many images, one submission** — `process_batch_image_edit_v1_image_edit_batch_post`
  (`POST /v1/image/edit/batch`). This is what the rate-limit documentation points you at when you
  need throughput, and it is far kinder to your quota than N async calls.

Both need a key scoped `image_editing`.

## Submit and read the acknowledgement

A valid submission returns an acknowledgement — not a result:

```json
{
  "data": {
    "id": 1,
    "status": "ACCEPTED",
    "result_url": "https://api.claid.ai/v1/image/edit/async/1",
    "created_at": "2023-03-13T11:37:09.160554+00:00",
    "request": { "...": "the body you sent" }
  }
}
```

`status` is always `ACCEPTED` for a valid submission. **Persist `id` before doing anything else** —
there is no idempotency key on this API, so if you lose the id you cannot recover the job, and
resubmitting bills you again.

`result_url` is the exact URL to poll; prefer it over reconstructing the path yourself.

## Poll for the result

Call `async_image_edit_result_v1_image_edit_async__task_id__get`
(`GET /v1/image/edit/async/{task_id}`), or
`image_edit_batch_async_results_v1_image_edit_batch__task_id__get`
(`GET /v1/image/edit/batch/{task_id}`) for a batch.

`status` moves `ACCEPTED` → `PROCESSING` → `DONE`. Poll with backoff — polling counts against the
same 120/minute and 4/second budget as the submissions do, so tight polling loops can rate-limit
the very jobs you are waiting on. A `404` here means the task id does not exist.

## Prefer webhooks over polling

Configure a webhook once at <https://claid.ai/account/webhook> and stop polling. Set the destination
URL, subscribe to **Success pipelines** and **Failed pipelines**, and set a shared secret to enable
signature verification.

Every delivery then carries:

```
X-Claid-Hmac-SHA256: <hmac-sha256-signature>
```

**Verify it before trusting the payload.** Read the *raw*, unparsed request body as a string,
compute the HMAC-SHA256 digest with the raw body as the message and your shared secret as the key,
and compare against the header. Reject the request if they differ. Comparing parsed-then-reserialised
JSON will not match — the digest is over the exact bytes.

The webhook body carries the same shape as the poll response, so one handler can serve both paths.

## Failure handling

Same envelope as everywhere else in this API: `error_code`, `error_type`, `error_message`,
`error_details`. `402` means the account is out of API call credits — halt the whole run rather
than grinding through the remaining images, since every one of them will fail the same way.

Log the `x-request-id` header per job; it is what <support@claid.ai> will ask for.
