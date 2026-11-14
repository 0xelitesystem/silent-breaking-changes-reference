# Silent Breaking Changes

A catalogue of five 2026 platform changes that reached working code without ever
returning an error, sorted by the only signal the caller actually had. Every
entry is quoted from a primary source and cited. `api-versioning-basics-reference`
teaches how to version an API you own; this is the receiving end, written for the
person whose code changed meaning while they were not looking.

Stated plainly up front: my own `openapi-changelog-diff` diffs two OpenAPI specs and
reports added, removed and changed operations, and it cannot see a single one of
these five, because none of them changes the schema. A spec diff is the wrong
instrument for this entire class of failure.

## The core idea

A breaking change is not defined by whether the schema moved, it is defined by
whether the caller's meaning changed, and the two come apart constantly. A filter
that switches from substring to whole-segment matching returns fewer rows under a
200. A beta header left in place after GA keeps serving the old body. A removed
feature can fall back quietly or return an error, and the same vendor picked both
answers twenty-five days apart. Sorting these by the signal you would have had,
rather than by vendor or date, is what turns a link roundup into something you
can act on.

## What is inside

- `01-no-signal-at-all.md` The MCP specification permits an authenticated
  `tools/list` result with a public `cacheScope` to be served to a different
  access token, and nothing reports it.
- `02-the-signal-was-a-log-line.md` electron-builder's GitHub publisher deletes a
  colliding release asset and re-uploads behind one `warn` line, exit code zero.
- `03-the-signal-was-fewer-rows.md` A dated beta header changes a path filter
  from substring to whole-segment matching and makes `order_by` a no-op.
  Announced July 2, 2026, triggered July 22, 2026.
- `04-the-signal-was-a-different-shape.md` After the Files API went GA on
  August 19, 2026, requests still sending the old beta header keep working and
  keep receiving the previous response format.
- `05-the-signal-was-on-the-invoice.md` Fast mode removed twice, twenty-five days
  apart, with opposite failure modes: a silent downgrade of speed and price on
  June 29, 2026, and a hard error that refuses to fall back on July 24, 2026.
- `06-the-detection-discipline.md` Six practices, each with the chapters it would
  have caught and the ones it would not.

## The stance

Deleting an unverifiable claim is always better than softening it. Every date,
header name, field name and quotation here was read out of a fetched primary
source at write time, and anything that could not be confirmed was cut rather
than hedged. Where a chapter discusses a named vendor or project, this reference
is independent of and unaffiliated with it, and describes only published
material. If a claim in here ever stops matching its source, the claim is wrong,
not the source.

## Privacy

This repository is plain Markdown. There is no code, no build step, no live demo,
no analytics, no tracking, and nothing to run. Reading it sends nothing anywhere.
The external links in the chapters are citations, and following one is your
choice, not something a page here does for you.

## Third-party notices

Short passages are quoted for identification and citation. No source project's
code or documentation is redistributed here in substantial part.

- electron-builder. Copyright holder, copied verbatim from its `LICENSE` at
  https://github.com/electron-userland/electron-builder/blob/master/LICENSE:
  "Copyright (c) 2015 Loopline Systems". MIT License. Taken: two short excerpts
  from `packages/electron-publish/src/gitHubPublisher.ts` (a two-line branch and
  one log statement) and the update file naming rule described in
  `packages/app-builder-lib/src/publish/updateInfoBuilder.ts`.
- Model Context Protocol specification, revision 2026-07-28,
  https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/caching.
  Its `LICENSE` at
  https://github.com/modelcontextprotocol/modelcontextprotocol/blob/main/LICENSE
  opens by stating that the project "is undergoing a licensing transition from
  the MIT License to the Apache License, Version 2.0", and carries an Apache-2.0
  grant followed by an MIT notice reading "Copyright (c) 2024-2025 Model Context
  Protocol a Series of LF Projects, LLC." Taken: one paragraph and two short
  sentences from the caching utility page.
- Claude Platform API release notes,
  https://platform.claude.com/docs/en/release-notes/api. Taken: short dated
  excerpts, quoted for citation. Published documentation, no licence file
  located; nothing beyond brief quotation is reproduced.

## Related

- https://github.com/0xelitesystem/api-versioning-basics-reference
- https://github.com/0xelitesystem/openapi-changelog-diff
- https://github.com/0xelitesystem/semantic-versioning-reference

## License

MIT. Copyright RBL Industries LLC 2026.
