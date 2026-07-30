---
name: Manage Claid storage connectors
description: >-
  Register and maintain AWS S3, Google Cloud Storage and web folder storage connectors on the Let's
  Enhance Claid API so processed images land in your own bucket instead of a temporary URL.
api: openapi/lets-enhance-claid-openapi.json
provider: Let's Enhance
base_url: https://api.claid.ai/v1/
scopes:
  - storage
operations:
  - list_storage_types_v1_storage_storage_types_get
  - list_storages_v1_storage_storages_get
  - create_storage_v1_storage_storages_post
  - get_storage_v1_storage_storages__storage_id__get
  - patch_storage_v1_storage_storages__storage_id__patch
  - delete_storage_v1_storage_storages__storage_id__delete
generated: '2026-07-19'
method: generated
source: https://docs.claid.ai/storage-connectors/overview
---

# Manage Claid storage connectors

By default Claid returns results as a temporary `tmp_url`. For any production pipeline you want
results written straight into your own storage instead — that is what connectors are for. This is
also the only genuine CRUD resource family in the API; everything else is a processing pipeline.

## Scope

These operations require the **`storage`** scope, which is the dashboard's *Storage* permission
group. A key scoped only to *Image editing* returns `403` here, and vice versa. Grant *Admin* only
when one key genuinely needs both.

## Discover the supported types

Call `list_storage_types_v1_storage_storage_types_get` (`GET /v1/storage/storage-types`) first
rather than hard-coding type strings. The documented types are AWS S3, Google Cloud Storage and web
folder.

## Register a connector

Call `create_storage_v1_storage_storages_post` (`POST /v1/storage/storages`) with the storage type,
a name and the credentials for that backend.

Two things to get right:

- **`name` is length-constrained.** The published validation example rejects names over 50
  characters with `error_code: 9000`.
- **Credentials are required per type** — the same example shows
  `parameters.credentials.access_key: field required`. Read `error_details` on a `422`; it names
  every missing or invalid field path.

Treat the credentials you send as write-only secrets on your side. Grant the bucket credentials the
narrowest permission that lets Claid write results.

## Read, update and remove

- `list_storages_v1_storage_storages_get` (`GET /v1/storage/storages`) — all connectors. There is no
  pagination; the full set is returned.
- `get_storage_v1_storage_storages__storage_id__get` (`GET /v1/storage/storages/{storage_id}`)
- `patch_storage_v1_storage_storages__storage_id__patch`
  (`PATCH /v1/storage/storages/{storage_id}`) — partial update; use this to rotate credentials
  rather than deleting and recreating.
- `delete_storage_v1_storage_storages__storage_id__delete`
  (`DELETE /v1/storage/storages/{storage_id}`)

A `404` on any of these means the storage id does not exist.

## Retry semantics

There is no idempotency key on this API. `POST /v1/storage/storages` is **not** safe to blindly
retry — a retried create that actually succeeded leaves you with a duplicate connector. On an
ambiguous failure, call `list_storages_v1_storage_storages_get` and reconcile before retrying.

`GET` and `DELETE` are naturally idempotent and safe to retry.

## Errors

Standard Claid envelope — `error_code`, `error_type`, `error_message`, `error_details`. The
storage endpoints return `401`, `403`, `404` and `422`; unlike the processing endpoints they do not
return `402` or `429`. Keep the `x-request-id` header for <support@claid.ai>.
