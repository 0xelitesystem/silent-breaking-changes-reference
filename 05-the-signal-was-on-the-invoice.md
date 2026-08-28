# 05. The signal was a line on the invoice

The same vendor removed the same feature from two adjacent models twenty-five
days apart, and chose opposite failure modes. The earlier model silently
downgraded both the service and the price. The later one returns an error and
explicitly refuses to fall back. This is the chapter to keep, because it shows
that "silent" is a decision, not an accident.

## Both receipts

From the Claude Platform API release notes at
https://platform.claude.com/docs/en/release-notes/api. This reference is
independent of and unaffiliated with that vendor.

June 29, 2026, quoted verbatim:

> We've removed fast mode for Claude Opus 4.6. Requests to `claude-opus-4-6`
> with `speed: "fast"` no longer run at fast speed or premium pricing: they run
> at standard speed, are billed at standard rates, and do not return an error.
> The response's `usage.speed` field reports the speed used.

July 24, 2026, quoted verbatim:

> We've removed fast mode for Claude Opus 4.7. Requests to `claude-opus-4-7`
> with `speed: "fast"` now return an error; unlike Claude Opus 4.6, they do not
> fall back to standard speed.

The July 24 removal was itself announced on June 25, 2026, which named the
removal date in advance.

## What each one costs you

The 4.6 path is the dangerous one, and not because of money. Your bill goes down,
which no alarm is watching for. What changes is latency: a request you sized a
timeout, a queue depth or a user-facing spinner around now takes standard-speed
time. If fast mode was load-bearing for a synchronous path, the breakage is a
p99 regression, and the only in-band evidence is `usage.speed`, a field written
for accounting that almost nobody reads.

The 4.7 path costs you an outage and buys you certainty. You find out on the
first request, at the point of the change, with a stack trace naming the
parameter.

## The lesson

An implicit downgrade is the more considerate option in the moment and the worse
one over a quarter. It converts a deploy-time failure into a slow degradation
whose cause is twenty-five days in the past by the time anyone plots it. If you
own an API, read that pair as a design menu: falling back quietly is a choice to
move the cost from your changelog onto your callers' dashboards.
