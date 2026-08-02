---
name: Discover the A2 Biotherapeutics API and MCP surface
description: >-
  Enumerate what A2 Biotherapeutics actually exposes — the WordPress REST route-discovery document,
  the site page inventory, and the two OAuth-gated Model Context Protocol servers — and establish
  what is reachable anonymously versus what needs authorization.
api: openapi/a2-biotherapeutics-wp-rest-openapi.yml
operations:
  - get
  - getWpV2Pages
  - getWpV2Types
  - getWpV2Taxonomies
  - getMcp
  - postMcpMcpOauthServer
  - getWpAbilitiesV1Abilities
generated: '2026-08-02'
method: generated
---

# Discover the A2 Biotherapeutics API and MCP surface

Honour the Content Signals Policy in `https://www.a2bio.com/robots.txt`
(`search=yes,ai-train=no,use=reference`) before anything else.

## Steps

1. **Read the discovery document.** Call `get` at `https://www.a2bio.com/wp-json/` (HTTP 200,
   anonymous). It returns `namespaces[]` and a `routes{}` map — 298 routes across 16 namespaces as of
   2026-08-02. This *is* the machine-readable contract; the provider publishes no OpenAPI
   (`/openapi.json`, `/swagger.json`, `/api-docs` all 404; `api.a2bio.com` does not resolve). The
   spec in `openapi/` was derived from this document.

2. **Separate core from plugin namespaces.** Only `wp/v2`, `wp-abilities/v1`, `mcp` and `oembed/1.0`
   are meaningful. The rest (`yoast/v1`, `cky/v1`, `redirection/v1`, `wordfence/v1`, `wp-rocket/v1`,
   `userway/v1`, `smart-slider-3/v1`, `ajaxselect2/v1`, `duplicate-post/v1`, `wp-site-health/v1`,
   `wp-block-editor/v1`) are vendor plugin internals — ignore them.

3. **Inventory the content model.** `getWpV2Types` at `/wp-json/wp/v2/types` and
   `getWpV2Taxonomies` at `/wp-json/wp/v2/taxonomies` return the registered post types and taxonomies.
   `getWpV2Pages` at `/wp-json/wp/v2/pages?per_page=100&_fields=id,slug,link,title` returns the whole
   site map — 26 pages covering science, pipeline, clinical trials, leadership, investors, careers,
   and the legal and California-compliance set. This is faster and more current than the site's
   `sitemap.xml`, which is a static 2022 export.

4. **Enumerate the MCP servers.** `getMcp` at `https://www.a2bio.com/wp-json/mcp` returns 200
   anonymously and lists three routes:
   - `/mcp` — the namespace index
   - `/mcp/mcp-oauth-server` — OAuth-guarded, POST/GET/DELETE
   - `/mcp/mcp-adapter-default-server` — capability-guarded, POST/GET/DELETE

5. **Expect a 401 on `tools/list`, and read the challenge.** `postMcpMcpOauthServer` with
   `{"jsonrpc":"2.0","id":1,"method":"tools/list"}` returns HTTP 401 and
   `{"code":"mcp_unauthorized",...}` plus a correct RFC 9728 challenge:

   ```
   WWW-Authenticate: Bearer realm="https://www.a2bio.com",
     resource_metadata="https://www.a2bio.com/.well-known/oauth-protected-resource"
   ```

   Follow that pointer. The protected-resource metadata names the resource and scope; the
   authorization-server metadata at `/.well-known/oauth-authorization-server` gives you
   `authorization_endpoint` `https://www.a2bio.com/oauth/authorize`, `token_endpoint`
   `https://www.a2bio.com/oauth/token`, PKCE `S256`, grants `authorization_code` + `refresh_token`,
   the single scope `mcp`, a public client (`token_endpoint_auth_methods_supported: ["none"]`), and
   `client_id_metadata_document_supported: true` — so register by publishing a client-ID metadata
   document rather than by calling a dynamic-registration endpoint.

6. **The tool set is not publicly knowable.** `getWpAbilitiesV1Abilities` at
   `/wp-json/wp-abilities/v1/abilities` — the WordPress Abilities registry that the MCP adapter
   projects into tools — also returns HTTP 401. There is no `llms.txt` (404) and no tool
   documentation. Do not guess tool names; enumerate them only after completing the OAuth flow. This
   is why `mcp/a2-biotherapeutics-tool-crosswalk.yml` records zero bound rows.

## What you will not find

No A2A agent card (`/.well-known/agent-card.json` and `/.well-known/agent.json` both 404), no GraphQL
(`/graphql` 404), no `security.txt` (404), no OpenID Connect discovery (404), no `api-catalog` (404),
no webhooks, no AsyncAPI, no SDKs on npm or PyPI, no CLI, no status page (`status.a2bio.com`
NXDOMAIN), no changelog, and no developer portal. A2 Biotherapeutics is a clinical-stage cell-therapy
company; it runs no API programme. Everything above is the incidental surface of its WordPress
corporate site.
