# 04. The signal was a response shape you never saw change

A beta header you forgot to remove does not stop working when the endpoint goes
GA. It keeps working, and it keeps serving you the old response body, so your
pagination contract freezes on a date you never noticed while everyone reading
current documentation moves on.

## The receipt

From the Claude Platform API release notes dated August 19, 2026, at
https://platform.claude.com/docs/en/release-notes/api. This reference is
independent of and unaffiliated with that vendor.

The note says the Files API is out of beta, that requests to the `/v1/files`
endpoints and Messages API requests referencing an uploaded file no longer
require the `files-api-2025-04-14` beta header, and that requests sent without
the header use the current response format: file expiration (set
`expires_in_seconds` on upload, file objects report `expires_at`), and `page` and
`next_page` pagination plus an `ids[]` filter when listing files.

Then the sentence that matters, quoted verbatim:

> `/v1/files` requests that still send the beta header keep working and return
> the previous response format.

A related note dated August 27, 2026 records that several SDKs stopped sending
`files-api-2025-04-14` from `client.beta.files`, and adds: "Requests that still
send the beta headers keep receiving the beta shapes."

## Why this reaches production

Everything about this is designed to be safe, and it is safe. Nobody's code
broke. That is exactly the problem: a stuck header produces no error, no
deprecation warning in the body, and no difference in status code. The endpoint
is simply serving two contracts, and you are on the old one.

The damage shows up second-hand. You read the current docs, write code against
`next_page`, and it is absent, because your client is still sending the beta
header three layers down in a shared HTTP wrapper. Or a new field like
`expires_at` never appears and you conclude the feature is not live for your
account. The GA behaviour is one header away, and the header is invisible at the
call site.

## The defence

Keep an inventory of every beta or preview header your code sends, with the date
you last reviewed it and the endpoint it applies to. A beta header is a pin to a
contract, and every pin needs a review date. Two greps across a codebase for the
header names is enough to build the list.
