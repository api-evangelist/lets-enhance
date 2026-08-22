# Let's Enhance

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
