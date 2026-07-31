# MacBridge — update artifacts

Signed release archives and the Sparkle appcast for
[MacBridge](https://github.com/superbam/MacBridge), whose source is private.

Public because it has to be: GitHub returns 404 for *anonymous* reads of a
private repo's raw files and release assets alike, so an updater with no stored
credential cannot fetch from one. Splitting the artifacts out keeps the source
private without putting a GitHub token inside a distributed app bundle, where
anyone holding the app would hold the credential.

Publishing the binaries costs nothing that matters. Every archive carries an
EdDSA signature that MacBridge verifies before installing, so hosting is a
question of reachability, not trust — a tampered archive fails the same check
whether it came from here or anywhere else.

## Layout

- `appcast.xml` — the Sparkle feed (`SUFeedURL` points at the `raw.` URL)
- Release **`artifacts`** — every published zip, as assets

One long-lived release rather than one per version, deliberately.
`generate_appcast` rewrites the enclosure URL of every archive it finds, using
a single prefix. A per-version tag puts the version *inside* that prefix, so a
previous release's zip present at generation time gets its URL silently
rewritten into the wrong tag — which happened twice during MacBridge's v0.1.2
and v0.1.3 releases, caught both times only by diffing before pushing, because
the signature covers file bytes and stays valid throughout. One flat prefix for
every version makes that rewrite produce the URL it already had.
