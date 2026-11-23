# Energon + MarkLayer source starter

This source-only integration uses Energon MIT and MarkLayer Apache-2.0. Ben Kochavy's integration additions are Apache-2.0; upstream terms remain intact. See NOTICE and the component licenses. No compiled widget, dependency bundle, credentials, production deployment configuration, customer data or private history is distributed here.

## Build and verify locally

Prerequisites: Git, Node 24+, npm, and Bun 1.3.14. From this starter directory:

```sh
STARTER="$(pwd -P)"
shasum -a 256 -c SHA256SUMS
git clone https://github.com/tmchow/energon.git energon
git -C energon switch -c marklayer-v15 a46ac3e2e18ae01e10abcf345e0da834a196505b
git -C energon apply --check "$STARTER/energon-marklayer-v15-source.patch"
git -C energon apply "$STARTER/energon-marklayer-v15-source.patch"

git clone https://github.com/thevrus/MarkLayer.git marklayer
git -C marklayer switch --detach 71ed772a840cacd6b0717b17156a4a04484c2b2f
git -C marklayer apply --check "$STARTER/marklayer-widget.patch"
git -C marklayer apply "$STARTER/marklayer-widget.patch"
(cd marklayer && bun install --frozen-lockfile && bun apps/extension/widget/build.ts "$STARTER/energon/public/static/comments/v15")
(cd energon && npm ci && npm test)
```

The two copies of the MarkLayer source patch (the standalone patch and the one the outer patch installs under Energon's vendor directory) are identical. The v15 location is new and the source-only release omits v1–v14 binaries. Do not use this starter to replace the historical assets of an existing instance; keep existing immutable version paths intact. Build output is local, not part of this repository. Before distributing or serving a generated bundle, retain the applicable licenses and notices of included dependencies. A standalone package lock is not a bundled-dependency attribution report.

For local development only, use the upstream Energon instructions, apply D1 migrations locally and use your own resources and credentials. Do not attach these files to an existing production Worker. Keep hosted feedback disabled unless deliberately enabled for eligible public HTML. MarkLayer hosts feedback data, uploads and realtime synchronization independently of Energon. This is not a self-hosting recipe for its backend.
