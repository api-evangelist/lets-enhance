# Let's Enhance

Let's Enhance is an AI image enhancement company. Its consumer product at
[letsenhance.io](https://letsenhance.io/) upscales, sharpens, denoises and restores photographs up to
512MP. Its developer platform ships as **[Claid.ai](https://claid.ai/)** — a REST API for AI image
editing, generation and image-to-video used by ecommerce, marketplace, real-estate and
print-on-demand teams to automate product imagery at scale. Let's Enhance is a Techstars-backed
company.

## API

The **Claid API v1** (`https://api.claid.ai/v1/`) is a declarative JSON pipeline API: a request
describes the input image, the operations to apply and the desired output. It covers synchronous and
asynchronous image editing, batch processing, direct upload, natural-language AI edit, AI fashion
models, AI background scene generation, image generation, image-to-video, and cloud storage
connectors for AWS S3, Google Cloud Storage and web folders.

- Documentation — <https://docs.claid.ai/>
- Quick start — <https://docs.claid.ai/quick-start>
- API reference — <https://docs.claid.ai/image-editing-api/api-reference>
- Status — <https://status.letsenhance.io/>

Authentication is a dashboard-minted API key sent as an HTTP Bearer token, scoped per key across
`image_editing`, `image_generation`, `video_generation` and `storage`. Long-running jobs are polled
by task id or pushed to an HMAC-SHA256-signed webhook.

## Artifacts

| Artifact | Path | Method |
|---|---|---|
| OpenAPI 3.1 (20 operations, 164 schemas) | `openapi/lets-enhance-claid-openapi.json` | searched |
| llms.txt | `llms/lets-enhance-llms.txt` | searched |
| Authentication profile | `authentication/lets-enhance-authentication.yml` | searched |
| OAuth scopes | `scopes/lets-enhance-scopes.yml` | searched |
| API conventions | `conventions/lets-enhance-conventions.yml` | searched |
| Rate limits | `rate-limits/lets-enhance-rate-limits.yml` | searched |
| Error codes + envelope | `errors/lets-enhance-error-codes.yml` | searched |
| Problem types | `errors/lets-enhance-problem-types.yml` | derived |
| Webhook catalog | `asyncapi/lets-enhance-claid-webhooks.yml` | searched |
| Lifecycle | `lifecycle/lets-enhance-lifecycle.yml` | searched |
| Conformance | `conformance/lets-enhance-conformance.yml` | derived |
| Data model | `data-model/lets-enhance-data-model.yml` | derived |
| Agentic access | `agentic-access/lets-enhance-agentic-access.yml` | generated |
| MCP tool surface (candidate) | `mcp/lets-enhance-mcp.yml` | derived |
| OpenAPI Overlay | `overlays/lets-enhance-claid-overlay.yaml` | generated |
| Agent Skills (5) | `skills/` | generated |
| Domain security | `security/lets-enhance-domain-security.yml` | probed |
| Well-known index | `well-known/lets-enhance-well-known.yml` | searched |

### Notable gaps

Recorded as honest negatives rather than omitted — Let's Enhance publishes **no** client SDKs or CLI,
no `/.well-known/` documents, no `security.txt`, no vulnerability disclosure programme, no trust
centre or named certifications, no dated changelog, no deprecation policy, no SLA, and no AsyncAPI
document (though a real webhook surface exists). The API has **no idempotency contract**, which
matters for retry safety on paid generation calls.

---

Backed by: techstars — <https://letsenhance.io/>
