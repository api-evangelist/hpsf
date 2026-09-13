---
name: hpsf-track-conferences
description: >-
  Track the High Performance Software Foundation's conference and event calendar — HPSFcon, SC, CppCon
  — from the foundation's own JSON API instead of scraping hpsf.io, and avoid the silent date window
  that hides every past event.
api: hpsf:hpsf-events-api
operations:
  - GET /events
  - GET /events/{id}
  - GET /events/by-slug/{slug}
---

# Track HPSF conferences

Base URL: `https://hpsf.io/wp-json/tribe/events/v1`. Anonymous, read-only, no key, no sign-up.
Verified 2026-09-13.

The contract this surface publishes is live at `https://hpsf.io/wp-json/tribe/events/v1/doc`
(OpenAPI 3.0.0). It declares no `operationId` for any operation, so refer to operations by method and
path.

## The one thing that will bite you

`GET /events` with no dates is **not** unbounded. The server silently applies
`start_date=<today> 00:00:00` and `end_date=<today+2 years> 23:59:59`, then reports the filter it
applied back to you in the response's `rest_url`. Observed 2026-09-13: a bare call returned `total: 2`
while the venue collection held 15 venues — because every past HPSFcon had been filtered out.

**Always pass `start_date` explicitly** when you want history:

    GET /events?start_date=2024-01-01 00:00:00&per_page=100

And **always read `rest_url`** in the response to confirm which window you actually got.

## Steps

1. **List upcoming events.**

       GET /events?per_page=100

   The envelope carries `total`, `total_pages`, `rest_url` and `next_rest_url`. Page by following
   `next_rest_url` verbatim — it is absolute and already carries the defaulted date window, so
   reconstructing page URLs by hand will drift.

2. **Read each event.** Useful fields: `title`, `slug`, `url` (the hpsf.io page), `website` (the
   conference's own site, e.g. `https://sc26.supercomputing.org/`), `all_day`, `utc_start_date`,
   `utc_end_date`, `timezone`, `cost`, `featured`, `venue`, `organizer`, `categories`, `tags`.

   Each date is published three ways — a local string, a UTC string, and a decomposed
   `{year,month,day,hour,minutes,seconds}` object. Read `utc_start_date` / `utc_end_date` and ignore
   the decomposed form.

3. **Fetch one event** by id or by slug:

       GET /events/714
       GET /events/by-slug/sc26

   A missing id returns HTTP 404 with `{"code":"rest-event-not-found", ...}`.

4. **Poll incrementally** by keeping the last `modified_utc` you saw and re-listing with an explicit
   `start_date`. There is no `modified_after` filter on this surface, so compare client-side.

## Calendar consumers should skip the API entirely

If the goal is to subscribe rather than to analyze, HPSF publishes the same calendar as RFC 5545
iCalendar at `https://hpsf.io/events/?ical=1` (`text/calendar`, HTTP 200). No code required. An
Outlook-flavoured variant is at `?outlook-ical=1`.

## Rules

- `per_page` maxes at 100 and is clamped, not rejected — `per_page=9999` returns 200 with a bounded
  page.
- `?_fields=` does **not** work on this surface; it returns an empty array. Responses are
  all-or-nothing.
- No rate-limit headers exist. Responses carry `cache-control: public, max-age=604800` from a Fastly
  edge with no `ETag` and no `Last-Modified`, so you may be served a week-old list and cannot
  revalidate cheaply. Poll daily at most; there is nothing here that changes hourly.
- Errors are the WordPress envelope `{code, message, data:{status}}` — not RFC 9457. Branch on `code`.
  See `errors/hpsf-problem-types.yml`.
- Read-only. Do not attempt writes: they need a WordPress Application Password that HPSF publishes no
  way for a third party to obtain.
- Do **not** use the `tec/v1` namespace. Its contract is richer but every route returns HTTP 400
  `missing_experimental_endpoint_acknowledgement` to an anonymous caller.
