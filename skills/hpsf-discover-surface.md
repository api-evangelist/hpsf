---
name: hpsf-discover-surface
description: >-
  Discover what hpsf.io actually serves to a machine — the RFC 9727 api-catalog, the llms.txt, the
  route index and two live OpenAPI documents — and, just as importantly, learn which advertised
  namespaces do not work before wasting calls on them.
api: hpsf:hpsf-site-discovery-api
operations:
  - GET /wp-json/
  - GET /.well-known/api-catalog
  - GET /llms.txt
  - GET /wp-json/tribe/events/v1/doc
  - GET /wp-json/tec/v1/docs
---

# Discover the HPSF machine surface

HPSF is a Linux Foundation project, not a software vendor: there is no developer portal, no API key,
no SDK and no documentation page. Everything a machine needs is nevertheless published — it is just
published as discovery documents rather than as docs.

## The discovery chain, in order

1. **`GET https://hpsf.io/.well-known/api-catalog`** → HTTP 200, `application/linkset+json`. An
   RFC 9727 linkset. It names exactly one service and anchors it at `https://hpsf.io/wp-json/`, with
   `service-doc` pointing at `developer.wordpress.org/rest-api/`. This is the canonical entrypoint.

   Verify it is real rather than a catch-all: every other `/.well-known/` path on this host returns
   404, including a control path that cannot exist. This host does not soft-200.

2. **`GET https://hpsf.io/wp-json/`** → HTTP 200. The route index: site identity, 15 registered
   namespaces, 99 route definitions with methods and argument schemas, and the Application Passwords
   authorization endpoint.

   **Trust the `routes` map, not the `namespaces` array.** They disagree on this host — see below.

3. **`GET https://hpsf.io/wp-json/tribe/events/v1/doc`** → HTTP 200, OpenAPI 3.0.0, 14 paths /
   30 operations. This is the contract for the surface you can actually call.

4. **`GET https://hpsf.io/llms.txt`** → HTTP 200, `text/plain`. HPSF's own llms.txt: posts, pages,
   projects (Kokkos, Wi4MPI, HPX, Flux Framework, Apptainer), venues, organizers, events, categories
   and project stages, each as a titled link. Good for orienting; it is a link index, not data.

5. **`GET https://hpsf.io/robots.txt`** → HTTP 200. Carries
   `Content-Signal: ai-train=yes, search=yes, ai-input=yes` — HPSF's machine-readable AI usage
   preference, granting all three. Respect it as stated.

## What is advertised but does not work

Check these once and stop retrying them:

- **`wp/v2`** — listed in the `namespaces` array at `/wp-json/` but absent from that same document's
  `routes` map. Every `wp/v2` path returns HTTP 404 `rest_no_route`. The WordPress content API is not
  served on this installation, so posts, pages, media, users and site search are **not** available as
  JSON. Use the RSS feed at `https://hpsf.io/feed/` for blog content instead.
- **`tec/v1`** — publishes the richer OpenAPI (3.0.4, 17 operations, named operationIds, declared
  BasicAuth) at `/wp-json/tec/v1/docs`, and then rejects every anonymous call with HTTP 400
  `missing_experimental_endpoint_acknowledgement`. The required header is named neither in the
  contract nor anywhere on hpsf.io. `/tec/v1/series` returns 401 on top of that.
- **`wp-abilities/v1`** — installed and registered, returns HTTP 401. The ability registry, which
  would be the closest thing here to a tool catalog, cannot be read anonymously.
- **`activity-log/v1`, `objectcache/v1`, `liquidweb/harbor/v1`, `regenerate-thumbnails/v1`,
  `wp-site-health/v1`, `tec/v2/onboarding`, `tribe/zapier/v1`, `tribe/power-automate/v1`,
  `tribe/event-aggregator/v1`** — site-administration and integration plugin routes, all gated to a
  logged-in WordPress administrator. They are an inventory of installed plugins, not a product
  surface.

## What actually works

`tribe/events/v1` — events, venues, organizers, categories and tags, anonymous and keyless. See
`skills/hpsf-track-conferences.md` and `skills/hpsf-map-venues.md`.

Plus two non-JSON surfaces that are often the better answer:

- `https://hpsf.io/events/?ical=1` — RFC 5545 iCalendar of the event calendar.
- `https://hpsf.io/feed/` and `https://hpsf.io/events/feed/` — RSS 2.0.

## Rules

- No authentication is available to you. Application Passwords are issued inside WordPress admin and
  HPSF publishes no third-party path to one, so treat the whole surface as read-only.
- No rate limits are published and no rate-limit headers are returned. Self-pace.
- There is no status page — the `status` link in the api-catalog linkset points at the homepage.
- Governance, not API, questions belong in the GitHub org: `https://github.com/hpsfoundation`
  (charter, TAC, lifecycle policy). Note that `https://github.com/hpsf` is an unrelated empty
  placeholder org.
