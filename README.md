# A2 Biotherapeutics

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
