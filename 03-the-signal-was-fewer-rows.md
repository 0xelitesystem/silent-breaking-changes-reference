# 03. The signal was fewer rows

A filter that used to match a substring started matching whole path segments,
which means the same request against the same data returns a smaller list and
never an error. This one was announced twenty days before it took effect, so the
silence is not in the communication. It is in the shape of the failure.

## The two dates

Both come from the Claude Platform API release notes at
https://platform.claude.com/docs/en/release-notes/api. This reference is
independent of and unaffiliated with that vendor.

Announced July 2, 2026. The note introduces the `agent-memory-2026-07-22` beta
header, which changes how listing memories
(`GET /v1/memory_stores/{memory_store_id}/memories`) behaves:

- results are returned in a stable, server-defined order, and the `order_by` and
  `order` parameters are ignored;
- `depth` accepts only `0`, `1`, or being omitted, and other values return a
  `400` error;
- `path_prefix` must end with `/` and matches whole path segments instead of a
  substring.

The same note adds that page cursors issued without the header are not valid with
it, that on memory store endpoints `agent-memory-2026-07-22` replaces
`managed-agents-2026-04-01`, and that sending both returns a `400` error.

Triggered July 22, 2026. Quoting the July 2 note: "On July 22, 2026, the
`managed-agents-2026-04-01` header adopts the same list behavior."

## Where the silence actually is

Three of these changes fail loudly or visibly. A bad `depth` returns a 400. Two
headers together return a 400. A stale cursor is rejected.

The `path_prefix` change does not. Before, `notes/proj` matched
`notes/project-alpha`. After, it matches only a whole segment. Your code sends
the same string, gets a 200, and gets fewer rows. If your caller treats an empty
or short list as "nothing to do", the pipeline just quietly does less work.

The ordering change is the same species. Code that passed `order_by` kept passing
it and kept getting a 200, but the parameter is now ignored. Anything that read
"first result" as "most recent" changed meaning without changing status code.

## The part that catches people

The trigger did not require a code change on your side. Callers still sending
`managed-agents-2026-04-01`, unchanged, got the new behaviour on a date. A dated
beta header is a scheduled contract change, not a pin, and treating it as a pin
is what turns an announced change into a silent one.
