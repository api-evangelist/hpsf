# High Performance Software Foundation (hpsf)

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

The High Performance Software Foundation (HPSF) is a Linux Foundation project and neutral hub for open
source high performance computing software, founded in 2024 and governed by a Governing Board, a
Technical Advisory Council (TAC) and an Outreach Committee. It hosts a portable core software stack for
HPC — including Spack, Kokkos, Apptainer, E4S, HPX, Charliecloud, Chapel, Viskores, Flux Framework and
Wi4MPI — across Core, Established and Emerging project stages, and runs HPSFcon alongside a presence at
SC and CppCon.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/hpsf/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Producing
- **Access:** 3rd-Party

## Tags

- HPC
- Linux Foundation
- Open Source
- Scientific Computing
- Foundation
- Supercomputing
- Open Governance
- Events
- Conferences
- Research Computing
- Nonprofit
- Content

## Timestamps

- **Created:** 2026-03-16
- **Modified:** 2026-09-13

## APIs

HPSF runs no developer program — no portal, no API key, no SDK, no documentation page — but `hpsf.io`
is a machine-readable surface in its own right, and an earlier version of this profile recorded it as
having none. What it actually publishes, all verified live on 2026-09-13:

- **An RFC 9727 `/.well-known/api-catalog`** linkset (HTTP 200, `application/linkset+json`) anchoring
  the WordPress REST API at `https://hpsf.io/wp-json/`. Confirmed a real document rather than a
  catch-all: every other well-known path, including a negative-control path that cannot exist, 404s.
- **Two live OpenAPI documents the host serves itself** — `/wp-json/tribe/events/v1/doc` (OpenAPI
  3.0.0, 14 paths / 30 operations) and `/wp-json/tec/v1/docs` (OpenAPI 3.0.4, 8 paths / 17
  operations). Both name `https://hpsf.io` in `servers[]`.
- **An `llms.txt`** (HTTP 200) listing HPSF's posts, pages, projects, venues, organizers, events and
  project stages.
- **A Content Signals directive in `robots.txt`** — `ai-train=yes, search=yes, ai-input=yes` — HPSF's
  own machine-readable AI usage preference.
- **An RFC 5545 iCalendar feed** at `/events/?ical=1`, which for a calendar consumer is the most
  directly useful thing on the host.

Three API entries are recorded: the **HPSF Events API** (`tribe/events/v1`, anonymous and keyless —
the events, venues and organizers behind HPSFcon), the **HPSF Events Calendar TEC v1 API**
(`tec/v1`, contract published but vendor-flagged experimental), and the **HPSF Site Discovery API**
(the `/wp-json/` route index).

Two honest negatives are recorded alongside them, because both are advertised and neither works:

- **`wp/v2` is declared in the namespace list at `/wp-json/` and is absent from that same document's
  route map.** Every `wp/v2` path returns `rest_no_route` 404. The WordPress content API is not served
  on this installation.
- **Every `tec/v1` route returns HTTP 400 `missing_experimental_endpoint_acknowledgement`** to an
  anonymous caller, and the required header is named neither in the contract the host serves nor
  anywhere on hpsf.io. The richer of the two contracts describes a surface a third party cannot invoke.

HPSF publishes no SDK, no CLI, no MCP server, no agent card, no webhook surface and no `security.txt`.
The contracts are authored by The Events Calendar plugin rather than by HPSF; they are recorded as
HPSF's because `servers[]` names HPSF's own host and the data described is HPSF's own — the
self-hosted case, not a sibling-product mix-up.

## Common Properties

- [Website](https://www.hpsf.io/)
- [Documentation](https://hpsf.io/projects/)
- [API Reference (live OpenAPI)](https://hpsf.io/wp-json/tribe/events/v1/doc)
- [GitHub Organization](https://github.com/hpsfoundation)
- [Blog](https://hpsf.io/blog/)
- [TAC / Governance](https://hpsf.io/tac/)
- [Charter](https://github.com/hpsfoundation/foundation/blob/main/charter.md)
- [Join / Membership](https://hpsf.io/join/)

> Note: `https://github.com/hpsf` is an empty placeholder organization with zero repositories and is
> **not** HPSF's. This profile previously pointed at it; the real org is
> [`hpsfoundation`](https://github.com/hpsfoundation).

## Maintainers

- **FN:** Kin Lane
- **Email:** info@apievangelist.com
