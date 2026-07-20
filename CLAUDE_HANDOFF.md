# Writer — Claude Handoff

Updated: 2026-07-20

## Mission

Writer is a local-first fiction studio for non-destructive manuscript work,
reviewable AI assistance, portable lore, and Davenport-compatible project
management.

The manuscript remains primary. AI output is always a proposal: it must be
reviewed and deliberately inserted. Opening an existing file creates a Writer
copy rather than overwriting the source.

Production site:
<https://storyberth-studio.d-the-designer.chatgpt.site/>

GitHub:
<https://github.com/D-the-Designer/writer>

## Current state

- The public GitHub repository contains the complete current source.
- Claude is a first-class provider through Anthropic's Messages API.
- Claude Sonnet 5 is the recommended quality route.
- Claude Fable 5, Opus 4.8, Sonnet 4.6, and Haiku 4.5 are also selectable.
- Long manuscripts, production bibles, lore, style guides, and sources use
  keyword-based passage retrieval in toolbar actions and AI sandbox chat.
- The retrieved context receipt identifies documents reduced to relevant
  excerpts.
- API keys are session-only unless the user explicitly enables browser-local
  storage. Keys are excluded from projects, exports, revisions, receipts, and
  Git.
- The full Playwright suite passes: 86 tests across desktop and mobile.
- Sites production version 6 has been built and saved but still requires
  explicit approval before public deployment.

## Important privacy boundary

Do not commit or deploy:

- manuscripts from browser storage;
- imported private lore or production bibles;
- API keys or provider credentials;
- browser storage, recovery snapshots, or privacy receipts;
- Sites source-repository credentials.

Company Man and the Softship Evidence Packet currently exist in the user's
Writer browser project, not in this Git repository.

## Repository map

- `writer/index.html` — application shell and menus.
- `writer/styles.css` — complete visual system and responsive layout.
- `writer/app.js` — state, persistence, editing, imports, AI providers,
  retrieval, revisions, exports, and UI behavior.
- `writer/tests/writer.spec.js` — release-contract Playwright tests.
- `writer/scripts/build-site.mjs` — creates the Cloudflare-compatible Sites
  package under `writer/dist/`.
- `writer/scripts/validate-knowledge-manifest.mjs` — validates Davenport
  knowledge manifests supplied as a command-line argument.
- `writer/schemas/davenport-knowledge-manifest.schema.json` — portable
  knowledge-manifest contract.
- `writer/knowledge/company-man-voice-guide.md` — non-manuscript house-voice
  guidance.
- `writer/.openai/hosting.json` — opaque Sites project identifier. Preserve it.
- `SECURITY.md` — credential and disclosure expectations.

## Development

```bash
cd writer
npm ci
npm test -- --reporter=line
npm run build
```

The knowledge validator requires a manifest path:

```bash
node scripts/validate-knowledge-manifest.mjs /path/to/davenport-knowledge-manifest.json
```

## Claude integration

The provider is `AnthropicProvider` in `writer/app.js`.

Endpoint:

```text
POST https://api.anthropic.com/v1/messages
```

The browser request uses:

- `x-api-key`
- `anthropic-version: 2023-06-01`
- `anthropic-dangerous-direct-browser-access: true`

The key lives in memory by default. If the user enables “Remember API keys,”
it is stored only in that browser under `writer.secret.anthropic`.

Do not move the key into project settings, environment exports, revision
records, chat exports, or Git.

## Context and lore behavior

Users explicitly select context through the Context picker. Each selected
document is visible before a request.

`retrieveRelevantPassages()` reduces documents longer than roughly 4,200
characters by scoring Markdown sections and paragraphs against meaningful
terms from the current instruction or chat message.

Both request paths must preserve this behavior:

1. toolbar writing operations through `buildRequest()`;
2. AI sandbox messages through `sendChat()`.

The earlier Groq failure was caused by sandbox chat sending the entire
56,585-word Company Man manuscript. That path is now covered by a regression
test and must not revert.

Desired future retrieval improvements:

- cap the combined context budget across multiple selected documents;
- index Markdown sections at import/save time;
- add aliases and Davenport tags to retrieval scoring;
- display the exact retrieved section names before submission;
- support pinned canon entries;
- warn when a draft conflicts with approved canon;
- keep local retrieval as the default, regardless of cloud provider.

## Product invariants

Do not weaken these without explicit approval:

1. Existing files open as new Writer copies.
2. AI cannot silently edit manuscript text.
3. Every transformation uses preflight and review.
4. Accepted changes append revision records.
5. Context and cloud scope remain visible.
6. Project data stays portable as Markdown plus optional manifests.
7. Davenport workflow state is separate from story canon status.
8. Adult-fiction tools remain available without pretending every provider has
   identical content policies.
9. The AI sandbox remains narrower on the left; the manuscript stays on the
   right.
10. No provider key enters a project, export, receipt, prompt log, or commit.

## Immediate handoff tasks

1. Obtain the user's explicit approval to deploy public Sites version 6.
2. After deployment, have the user enter an Anthropic API key in
   `Tools → Providers & models… → Claude API`.
3. Select Claude Sonnet 5 and run a connection-only test without manuscript
   context.
4. Select the Softship Evidence Packet and verify a lore-grounded response.
5. Select Company Man and confirm the request receipt says
   `relevant excerpts` rather than sending the entire manuscript.
6. If direct Anthropic browser requests fail because of CORS or account policy,
   preserve the UI and route Claude through a small server-side proxy that
   keeps user keys out of logs and persistent storage.

## Deployment notes

The Sites project is public. Deploying a saved version changes the production
site and requires explicit user approval at action time.

Version 6 was created from the tested Claude migration commit. Do not create a
new Sites project, change the project identifier, or deploy an unrelated Git
state.

## Definition of done for the migration

- Version 6 is live at the existing production URL.
- The user can enter a Claude key without exposing it in chat.
- Claude returns a connection-test response.
- Lore questions use selected lore excerpts.
- Company Man questions use bounded manuscript excerpts.
- The full automated test suite still passes.
- GitHub and deployed source describe the same behavior.
