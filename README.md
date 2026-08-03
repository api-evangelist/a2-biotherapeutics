# A2 Biotherapeutics

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
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

A2 Biotherapeutics (A2 Bio) is a clinical-stage biotechnology company in Agoura Hills, California,
founded in 2018 by Alexander Kamb, Michael Gallo and Paul Kang. It develops logic-gated cell therapies
for solid tumors on its proprietary Tmod platform — a two-receptor activator/blocker design that lets
an engineered T cell attack tumor cells which have lost an HLA allele while sparing normal cells that
retain it.

- https://www.a2bio.com/

## API posture

**A2 Bio runs no developer API programme.** There is no developer portal, no documentation, no SDKs,
no CLI, no sandbox, no status page and no published OpenAPI.

What it does operate — verified live on 2026-08-02 — is the WordPress REST API of its corporate
website, and two Model Context Protocol servers on the same install:

| Surface | URL | Status |
|---|---|---|
| REST route discovery | `https://www.a2bio.com/wp-json/` | 200 anonymous — 298 routes, 16 namespaces |
| MCP namespace index | `https://www.a2bio.com/wp-json/mcp` | 200 anonymous |
| MCP OAuth server | `https://www.a2bio.com/wp-json/mcp/mcp-oauth-server` | 401 — OAuth 2.1, scope `mcp` |
| MCP adapter default server | `https://www.a2bio.com/wp-json/mcp/mcp-adapter-default-server` | 401 — capability-gated |
| Abilities registry | `https://www.a2bio.com/wp-json/wp-abilities/v1/abilities` | 401 |
| OAuth AS metadata (RFC 8414) | `/.well-known/oauth-authorization-server` | 200 |
| OAuth PR metadata (RFC 9728) | `/.well-known/oauth-protected-resource` | 200 |

The MCP tool set could not be enumerated — `tools/list` is auth-gated on both servers and the
abilities registry it projects from is gated too — so `mcp/a2-biotherapeutics-tool-crosswalk.yml`
records zero bound tools rather than guessing at names.

`openapi/a2-biotherapeutics-wp-rest-openapi.yml` (136 paths, 292 operations) is **derived by API
Evangelist** from the provider's own live route-discovery document, which is saved verbatim alongside
it. A2 Bio publishes no OpenAPI of its own.

## Access posture

`https://www.a2bio.com/robots.txt` carries a Content Signals Policy —
`search=yes,ai-train=no,use=reference` — declared as an express reservation of rights under Article 4
of EU Directive 2019/790, plus named `Disallow` rules for GPTBot, ClaudeBot, Google-Extended,
Amazonbot, Applebot-Extended and meta-externalagent. Honour it.

## Compliance

A2 Bio publishes a genuine regulatory compliance declaration at
https://www.a2bio.com/california-compliance/ covering the HHS OIG Compliance Program Guidance for
Pharmaceutical Manufacturers, California Health and Safety Code §§119400–119402, and the AdvaMed Code
of Ethics. This is pharmaceutical-sector compliance, not an information-security attestation — no SOC
2, ISO 27001 or trust centre exists.
