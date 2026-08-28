# 01. The signal was nothing at all

The Model Context Protocol specification allows the result of an authenticated
`tools/list` call to be cached by a client and served outside the authorization
context that produced it, and nothing in the exchange tells either side. No
status code changes, no header changes, no error.

## The clause

The caching utility page of the 2026-07-28 specification revision defines a
`cacheScope` field on a response. Quoted verbatim from
https://modelcontextprotocol.io/specification/2026-07-28/server/utilities/caching:

> Servers MUST be aware that responses with a "public" `cacheScope` may be
> shared between callers even if the Result is coming from an authenticated
> endpoint. For example, the Result from an authenticated `tools/list` call with
> a "public" `cacheScope` may be cached by a client and may be shared outside of
> the initial requests authorization context. (i.e. different access tokens can
> leverage the same cache).

The page's value table says `"public"` means any client, shared gateway, or
caching proxy MAY store and serve the response to any user, while `"private"`
means caches MUST NOT be shared across authorization contexts.

## Why this is silent

Your server authenticated the request and returned a correct answer for that
token. The sharing happens later, for someone else's token. There is no round
trip to instrument, because from your side there is no second round trip: the
second caller never reaches you.

The failure mode is a tool list tailored to one tenant handed to another. If your
`tools/list` result varies by principal (per-customer integrations, role-gated
tools, tenant-specific tool names), labelling it `"public"` is a disclosure that
returns 200 to everyone involved.

## What the spec asks of you

The duty sits on the server. Server implementors "should ensure that the
`cacheScope` correctly reflects the intended visibility of the primitive" and
"MUST apply appropriate per-primitive access controls, and MUST NOT rely on
`cacheScope` alone to prevent unauthorized access to primitives."

`cacheScope` is a caching hint, not an authorization boundary. If a tool must
not be visible to a token, the check belongs on the call. The page also requires
scope consistency across pages of one list request.

Default to `"private"` for anything an authenticated endpoint produced, and
promote to `"public"` only for a response you would publish. That is a one-word
decision with a blast radius no log shows you.
