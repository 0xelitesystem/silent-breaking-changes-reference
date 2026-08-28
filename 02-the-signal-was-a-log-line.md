# 02. The signal was one log line

When electron-builder's GitHub publisher finds that an asset with the same file
name already exists on a release, it does not fail the publish. It deletes the
existing asset, uploads yours over the top, and records the whole event as a
single `warn` line. The build exits zero.

## The code path

Source, electron-builder master:
https://github.com/electron-userland/electron-builder/blob/master/packages/electron-publish/src/gitHubPublisher.ts

The upload catch handler routes a collision into `overwriteArtifact`:

```
} else if (this.doesErrorMeanAlreadyExists(e)) {
  return this.overwriteArtifact(fileName, release).then(() => this.doUploadFile(attemptNumber + 1, parsedUrl, fileName, dataLength, requestProcessor, release))
```

`doesErrorMeanAlreadyExists` returns true when GitHub answers 422 with an
`already_exists` error code. `overwriteArtifact` then logs, lists the release's
assets, and issues a `DELETE` against the one whose `name` matches:

```
log.warn({ file: fileName, reason: "already exists on GitHub" }, "overwrite published file")
```

That string is the entire notification. Control returns to `doUploadFile` with
an incremented attempt number and retries.

## Why this is easy to miss

The word "overwrite" does a lot of work in a line most CI systems fold into a
collapsed step. Nothing sets a non-zero exit code and nothing annotates the
release. The published artifact is the last one written.

The scenario that bites: two jobs publishing to the same tag, or a re-run of one
that already published. Both produce a file with the same name, the second run
wins, and the binary users download is whichever finished last. The release page
shows one asset of the expected name, so it looks right.

For auto-updating apps there is a second-order effect. In
`packages/app-builder-lib/src/publish/updateInfoBuilder.ts`, the update metadata
file name is built from the channel (default `latest`), an OS suffix and an arch
prefix, with no version in it. So it collides on a re-publish exactly like a
binary does, and metadata describing build A can end up beside build B.

## The defence

Treat the release tag as write-once. Fail the pipeline if the tag already has
assets rather than letting the publisher reconcile it. Failing that, grep the
publish log for `overwrite published file` and fail on a match: one grep turns
the warn into the error it should have been.

Independent description of published source; see the README's notices.
