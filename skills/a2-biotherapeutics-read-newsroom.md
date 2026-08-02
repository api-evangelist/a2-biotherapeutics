---
name: Read the A2 Biotherapeutics newsroom
description: >-
  Retrieve A2 Biotherapeutics corporate press releases — including clinical trial readouts — from the
  live WordPress REST API on www.a2bio.com, with correct pagination and payload trimming.
api: openapi/a2-biotherapeutics-wp-rest-openapi.yml
operations:
  - getWpV2Posts
  - getWpV2PostsById
  - getWpV2Categories
  - getWpV2Media
generated: '2026-08-02'
method: generated
---

# Read the A2 Biotherapeutics newsroom

## Before you start — the provider's access posture

A2 Biotherapeutics publishes a Content Signals Policy in `https://www.a2bio.com/robots.txt`:

```
Content-Signal: search=yes,ai-train=no,use=reference
```

It is declared as an express reservation of rights under Article 4 of EU Directive 2019/790, and the
same file explicitly `Disallow`s GPTBot, ClaudeBot, Google-Extended, Amazonbot, Applebot-Extended and
meta-externalagent. **Use this content for reference and citation only. Do not use it to train or
fine-tune a model.** If you are one of the named agents, do not fetch at all.

## Auth

None required. Published posts are readable anonymously over HTTPS. Do not send credentials.

## Steps

1. **List the newsroom.** Call `getWpV2Posts` against
   `https://www.a2bio.com/wp-json/wp/v2/posts`. Trim the payload with `_fields` — the full `content`
   of every post is large and full of page-builder shortcodes:

   ```
   GET /wp-json/wp/v2/posts?per_page=20&_fields=id,date,slug,link,title,excerpt
   ```

2. **Paginate properly.** Read `X-WP-Total` and `X-WP-TotalPages` from the response headers, and the
   `Link: <...>; rel="next"` header, rather than guessing. `per_page` maxes out at 100; the default is
   10. As of 2026-08-02 there were 28 posts. Never assume a single page is the whole newsroom.

3. **Narrow by date when you want recent news.** `after` and `before` take ISO 8601 datetimes, and
   `orderby=date&order=desc` is the natural sort:

   ```
   GET /wp-json/wp/v2/posts?after=2026-01-01T00:00:00&orderby=date&order=desc&_fields=id,date,title,link
   ```

4. **Fetch one release in full** with `getWpV2PostsById` at
   `/wp-json/wp/v2/posts/{id}`. The `content.rendered` field is HTML that contains WPBakery
   `[vc_row]` / `[vc_column_text]` shortcodes — strip both the shortcodes and the HTML tags before
   using the prose. `excerpt.rendered` is a cleaner short summary when it is populated.

5. **Resolve related resources with `_embed`** rather than extra round trips. Adding `_embed=1`
   inlines the author, featured media and terms named in the `_links` envelope. If you want the images
   separately, `getWpV2Media` at `/wp-json/wp/v2/media` accepts `?parent={post_id}`.

6. **Group by topic** with `getWpV2Categories` at `/wp-json/wp/v2/categories`, then filter posts with
   `?categories={id}`.

## Conventions to respect

- **Pagination:** `page` / `per_page` / `offset`; totals in `X-WP-Total` and `X-WP-TotalPages`.
- **Sparse fields:** `_fields` is a comma-separated allowlist and materially reduces response size.
- **Errors:** the envelope is `{"code": ..., "message": ..., "data": {"status": ...}}` — *not*
  RFC 9457 problem+json. Branch on `code`, never on `message`. See
  `errors/a2-biotherapeutics-problem-types.yml`.
- **No rate-limit signal:** the API sends no `RateLimit-*` or `Retry-After` headers. Cloudflare fronts
  the origin and may throttle silently, so be conservative — serial requests, modest `per_page`, and
  back off on any non-2xx.
- **No idempotency contract exists.** Irrelevant here because this skill is read-only; it matters if
  you ever attempt a write.

## Scope limit — read this before answering a clinical question

This API serves the corporate marketing site. It exposes press releases, site pages, and media. It
exposes **no** clinical trial data, no patient data, no assay or sequence data, and no research
datasets. A2 Biotherapeutics publishes no product or research API of any kind. For trial data, go to
ClinicalTrials.gov and the peer-reviewed publications listed at
`https://www.a2bio.com/science/abstracts-and-publications/`; treat press releases as company
statements, not as primary clinical evidence.
