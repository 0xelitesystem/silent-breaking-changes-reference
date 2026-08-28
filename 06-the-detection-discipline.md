# 06. The detection discipline

No single practice catches all five receipts, and any list that claims otherwise
is selling something. Below is each practice with the chapters it would have
caught and, more usefully, the ones it would not.

## Pin response shapes in tests

Assert the full field set of one real response per endpoint, including fields
you do not use, and require the ones you do.

Catches chapter 04, but only if the assertion is "`next_page` must be present",
not "unknown fields are allowed". A stuck beta header removes fields; strict
unknown-field parsing only fires when fields are added. Misses 01, 02, 05.

## Assert ordering explicitly

If you rely on order, assert it in a test with more than two rows and never rely
on insertion order holding.

Catches the `order_by` half of chapter 03, where the parameter began being
ignored under a 200. Misses everything else, including the `path_prefix` half of
the same change.

## Alarm on row-count deltas

Track the number of rows each scheduled job processes and alarm on a step change
against the trailing window, not on zero.

Catches the `path_prefix` substring-to-segment change in chapter 03, which is the
one receipt here that returns a smaller correct-looking list. Misses 01, 02, 04,
05. Note that alarming only on zero would have missed it too.

## Pin beta and preview headers to a dated review

Keep a list of every beta header your code sends, the endpoint it applies to,
and the date you last reviewed it. Treat a dated header as a scheduled change.

Catches 03 and 04, the two receipts that are entirely about headers. Misses 01,
02, 05.

## Fail the release when the publisher reconciles

Make release tags write-once, and fail the pipeline on the publisher's overwrite
warning rather than reading it.

Catches 02 and nothing else. Worth doing anyway, because it is one grep.

## Watch latency and usage fields, not just cost

Alarm on p99 latency per model and per route, and record any `usage` field the
provider returns.

Catches 05, where the bill fell and the latency rose. A cost alarm alone would
not have fired, because the silent path billed less.

## The one nothing here catches

Chapter 01 has no client-side detection. The sharing happens in another process,
for another token, and never reaches your server. The only control is the
default: label authenticated responses `"private"` and enforce per-call
authorization instead of relying on a cache label. Detection is not available;
prevention is.
