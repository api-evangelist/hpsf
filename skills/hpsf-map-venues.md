---
name: hpsf-map-venues
description: >-
  Build the geography of where the High Performance Software Foundation meets — 15 geocoded venues
  across the US, Europe and Canada — from the foundation's own venue collection, and join it back to
  the events held there.
api: hpsf:hpsf-events-api
operations:
  - GET /venues
  - GET /venues/{id}
  - GET /venues/by-slug/{slug}
  - GET /organizers
  - GET /events
---

# Map the HPSF venue footprint

Base URL: `https://hpsf.io/wp-json/tribe/events/v1`. Anonymous, read-only, no key. Verified
2026-09-13.

The venue collection is the most substantive data on this host. On 2026-09-13 it held **15 venues**
against **2 currently-listed events** — because events expire out of the default date window and
venues do not. It is the durable record of HPSF's meeting geography: Aurora Colorado, Montreal,
Chicago, the University of Bern & PHBern, the Technical University of Braunschweig, Maison de la
Simulation at Saclay, and others.

## Steps

1. **Enumerate the venues.**

       GET /venues?per_page=100

   The envelope carries `total` and `total_pages`. Each venue has `id`, `global_id`, `slug`, `venue`
   (the display name), `url` (its hpsf.io page), `city`, `state`, `stateprovince`, `country`,
   `show_map`, and — where HPSF geocoded it — `geo_lat` and `geo_lng`. Not every venue carries
   coordinates; check before plotting rather than assuming.

2. **Fetch one venue** by id or slug:

       GET /venues/967
       GET /venues/by-slug/aurora-colorado

3. **Join events to venues.** An event embeds its venue as a **full object**, not a bare id, so a
   single `GET /events` pass gives you the event→venue edges with no follow-up requests. To go the
   other way — every event at a given venue — filter events by venue id **with an explicit history
   window**:

       GET /events?venue=276&start_date=2024-01-01 00:00:00&per_page=100

   Without `start_date` the server applies a today-forward window and you will conclude, wrongly, that
   a venue has never hosted anything.

4. **Organizers** are a separate collection (`GET /organizers`). On 2026-09-13 there was exactly one:
   HPSF itself. The schema allows `phone` and `email` on an organizer and serves them anonymously when
   populated — treat organizer records as potentially personal data even though these are not.

## Rules

- Identifiers are site-local WordPress post ids drawn from the same sequence as pages and posts. Key
  on `global_id` (`hpsf.io?id=967`) if you need something stable across installations.
- Venue counts and the venue list are a snapshot; re-read rather than caching indefinitely. There is
  no `ETag` or `Last-Modified` to revalidate against, and the edge caches for seven days.
- The `/categories` and `/tags` taxonomies are registered and callable but **empty** on this
  installation (`total: 0`). A 200 with zero results is the correct answer, not a failure.
- Read-only. See `conventions/hpsf-conventions.yml`.
