# Gameball

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

Gameball is a customer loyalty and gamification platform that helps consumer brands turn one-time buyers into repeat customers through points, VIP tiers, referrals, cashback, coupons, reward campaigns and gamified experiences (spin the wheel, scratch & win, missions, stamps) plus email/SMS/push communication and automation campaigns. It exposes a RESTful API (base https://api.gameball.co/api/{version}) secured with APIKey and SecretKey headers — v4.1 "Secure Integration Mode" is the documented current release, though the only OpenAPI Gameball publishes is v4.0. Alongside it are server SDKs for Node.js, Python, PHP, Ruby and .NET, actively-maintained mobile client SDKs for iOS, Android, Flutter and React Native, an embeddable web widget, signed webhooks, asynchronous batch ingestion, a test/live dual-key sandbox, an official OAuth-gated remote MCP server at https://mcp.gameball.co exposing 54 dashboard tools, a published A2A agent card and a provider-authored Agent Skill. Gameball is trusted by 3,000+ brands across 70+ countries and is a 500 Global portfolio company.

**Areas:** Company · Loyalty · Rewards · Gamification · Customer Engagement · Retention · Referrals · Marketing · E-Commerce · A2A · Loyalty & Incentives

## Gameball on APIs.io

Everything profiled for Gameball is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/gameball/](https://apis.io/providers/gameball/)** — or go straight to one of the APIs or properties below.

### APIs (9)

- [Gameball MCP Server](https://apis.io/apis/gameball/gameball-mcp-server/) — Official remote Model Context Protocol server exposing 54 tools over the Gameball merchant/dashboard surface — customer lookup and points adjustment, tag…
- [Gameball Batches API](https://apis.io/apis/gameball/gameball-batches-api/) — The Batches API from Gameball — 1 operation(s) for batches.
- [Gameball Customers API](https://apis.io/apis/gameball/gameball-customers-api/) — The Customers API from Gameball — 1 operation(s) for customers.
- [Gameball Events API](https://apis.io/apis/gameball/gameball-events-api/) — The Events API from Gameball — 1 operation(s) for events.
- [Gameball API](https://apis.io/apis/gameball/gameball-gameball-api-api/) — The Gameball API API from Gameball — 0 operation(s) for gameball api.
- [Gameball Integrations API](https://apis.io/apis/gameball/gameball-integrations-api/) — The Integrations API from Gameball — 62 operation(s) for integrations.
- [Gameball Orders API](https://apis.io/apis/gameball/gameball-orders-api/) — The Orders API from Gameball — 4 operation(s) for orders.
- [Gameball Payments API](https://apis.io/apis/gameball/gameball-payments-api/) — The Payments API from Gameball — 1 operation(s) for payments.
- [Gameball Plants API](https://apis.io/apis/gameball/gameball-plants-api/) — The Plants API from Gameball — 2 operation(s) for plants.

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Gameball Webhooks](https://apis.io/asyncapis/gameball/gameball-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [gameball-mcp](https://apis.io/servers/gameball/gameball-mcp/)

#### Collections (1)

Postman and Open Collection request collections.

- [Gameball API](https://apis.io/collections/gameball/open-gameball/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Gameball Openapi Overlay](https://apis.io/overlays/gameball/gameball-openapi-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Gameball Authentication](https://apis.io/security/gameball/gameball-authentication/)
- [Gameball Domain Security](https://apis.io/security/gameball/gameball-domain-security/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Gameball Scopes](https://apis.io/scopes/gameball/gameball-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Gameball Plans Pricing](https://apis.io/plans/gameball/gameball-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Gameball Rate Limits](https://apis.io/rate-limits/gameball/gameball-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://gameball.co)
- [Documentation](https://docs.gameball.co)
- [Getting started](https://docs.gameball.co/product-documentation/getting-started/launch-checklist)
- [API reference](https://docs.gameball.co/api-reference/introduction)
- [Pricing](https://www.gameball.co/pricing)
- [Sign up](https://app.gameball.co/login)
- [Support](https://docs.gameball.co/)
- [Changelog](https://docs.gameball.co/changelog/updates)
- [Blog](https://www.gameball.co/blog)
- [Terms of service](https://www.gameball.co/terms-and-conditions)
- [Privacy policy](https://www.gameball.co/privacy-policy)
- [GitHub](https://github.com/gameballers)
- [Status](https://gameball.statuspage.io)

## API Evangelist rating

**Kin Score 56.5** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 71.7 |
| Contract quality | 43.3 |
| Contract governance | 4.5 |
| Operational transparency | 73.7 |
| Developer ergonomics | 78.6 |
| Access clarity | 52.6 |

**Agent readiness 37.9** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | — |
| Reversibility Documented | documented (partial credit) |
| MCP Server | documented (partial credit) |
| Auth Clarity | bearer (partial credit) |
| Idempotency | documented (partial credit) |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | yes |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | yes |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | — |
| Operation Distinctiveness | — |

**Regulatory alignment 34.3** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/gameball/](https://apis.io/providers/gameball/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Gameball: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/gameball/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
