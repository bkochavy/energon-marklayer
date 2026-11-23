# Energon + MarkLayer source starter

This source-only integration uses Energon MIT and MarkLayer Apache-2.0. Ben Kochavy's integration additions are Apache-2.0; upstream terms remain intact. See NOTICE and the component licenses. No compiled widget, dependency bundle, credentials, production deployment configuration, customer data or private history is distributed here.

## Copy this prompt to your coding agent

> Set up my own Energon instance with the MarkLayer v15 integration in this repository. Read this README, verify the source checksums, clone the full public upstream histories at the exact pins below, apply both patches, build the v15 widget locally, and run the local tests. Stop after local validation unless I explicitly approve the deployment and identify the destination and resources. Do not reuse anyone else's settings, data, tokens, domains, or cloud resources. If approved, use my own Cloudflare account and a fresh instance, keep automatic deployment off, and have me complete sign-in and access approvals directly. Use only synthetic public HTML for hosted feedback checks. Report what you verified locally, what you verified against the hosted service, and whether anything was deployed. Do not claim parity for tests you did not run.

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


## Configure a fresh deployment only after approval

1. **Confirm scope.** Identify the recipient's separate private deployment checkout, Cloudflare account, Worker, D1 database, R2 bucket, hub and content hostnames, sign-in policy, and plugin identity. Leave automatic deployment off. Use the patched checkout's `INSTALL.md` and `docs/DEPLOY.md`. If an upstream helper creates another checkout, apply the source patch and build the v15 widget again in the exact checkout that will deploy. Preserve public upstream ancestry. Do not copy deployment settings or production resources into the new instance.
2. **Keep credentials local to the new instance.** Have the owner complete Cloudflare sign-in, select an identity policy, and approve any Energon agent connection. Follow the instance's `/auth.md` for agent access. Use the owner's secret storage; do not commit or paste tokens. Verify the Worker and bindings before applying the scoped D1 migrations, including 0027–0029. Regenerate the agent plugin from the patched templates for the new instance.
3. **Enable optional hosted feedback deliberately.** Set `COMMENTS_ENABLED=true` only for eligible public HTML after approving use of the hosted service. The default is off. Normal MarkLayer feedback needs no separate MarkLayer account or API key. For optional reviewer-name memory, apply migration 0029 and provide a dedicated random `COMMENTS_IDENTITY_SECRET` of at least 32 characters as a Worker secret. Ordinary reviews must still work without it.
4. **Respect hosted storage.** Room data, uploads and realtime sync stay at `marklayer.app`. Energon permissions, backups and retention do not govern that hosted data; already-shared concrete room links may continue to work after Energon access changes. This starter does not deploy a MarkLayer backend, migrate rooms, or reproduce another instance's configuration or data.

## Install the agent skill

The source patch ships the **templates** for the instance-specific publishing and feedback skill, not a prebuilt plugin or access to an existing instance. In your configured deployment checkout, run `npm run skill:render` to generate the skill and its API reference. Use your own instance's `GET /v1/help` (`install`) or `/setup` to get the exact repository and skill name.

> Install the generated Energon skill from my instance's deployment repository at user scope, using the repository and skill name shown by my instance. Ask me to approve the connection from its `/auth.md`. Never send my Energon token to MarkLayer.

For clients supporting the Skills CLI: `npx skills add OWNER/DEPLOYMENT-REPO --skill GENERATED-SKILL-NAME -g`. Compatible clients can also install the generated plugin through their marketplace. For private repositories or other clients, follow the patched `INSTALL.md` for authenticated local-skill or API-only fallback; do not make the deployment repository public to install it.

The generated skill covers publishing and ensuring a shared room (`POST /v1/comments` with `action:"ensure"`), authenticated room lookup (`GET /v1/comments?url=...`), reading the returned hosted feedback link without the Energon credential, and starting a fresh round when authorized (`action:"new"` with an idempotency key).

## Smoke-test the instance before claiming parity

Run these checks on an explicitly approved fresh deployment with a disposable public HTML page you own. Use synthetic text, never customer material. Allow for the fact that viewing an eligible feedback URL may create a hosted room. Do not expose tokens in screenshots, logs, or browser URLs.

| Check | Expected result |
| --- | --- |
| Clean URL (no comment parameter) | Ordinary page, no injected review widget. |
| `?comments=1` | Finds the current review room or can provision one when first opened, including by a signed-out visitor, for an eligible public page. Open in two separate signed-out browser contexts; post a synthetic text highlight and a pin, and verify readback in the other. Bare or empty `comments` and aliases `comment` and `feedback` select the same mode; use canonical `comments` in shared links. |
| `?comments=ROOM_ID` | Opens that concrete room. Its link still points to the earlier feedback after a new current room is created. |
| `?comments=new` | A signed-in actor with write permission can explicitly confirm creating a fresh round. Opening the URL alone must not create it. Prior feedback stays in the old room while the new one becomes current. For the equivalent authorized API operation, `POST /v1/comments` uses an idempotency key reused on retries. |
| Authenticated agent lookup | With the instance's authorized Energon credential, call `GET /v1/comments?url=...`. Verify it returns room metadata and that the returned read link exposes the synthetic feedback. Lookup must not create a room. A signed-out commenter does not gain Energon publish, edit, access-management or fresh-round privileges. |
| Duplicate versus new round | Duplicate the fixture to a new Energon address and verify an independent room is used there. A new round changes the current room on the original page; ordinary edits to that page should not create new rounds. |
| Exclusions | Private or password-protected HTML, Markdown, static assets and download/export responses do not acquire a widget. |
| Optional name memory | If configured, save a synthetic reviewer name and confirm it appears on another eligible review link in the same browser. If not configured, ordinary commenting must still work. |

For the detailed test sequence, see `.agents/skills/verify-energon/features/comments.md` in the patched checkout. Treat unavailable hosted endpoints as blocked, not as passing or empty. Local build/test success does not verify a live deployment or the hosted workflow.
