# Morpheus Onboarding Prototype — Project Context

This file is read automatically by Claude Code at the start of a session in this
repo. It exists so context from prior planning sessions isn't lost when switching
tools — keep it updated as the project evolves.

## What this is

A local Mintlify prototype for `onboarding.mor.org` / `guides.mor.org` — a guided
onboarding layer sitting above the existing docs stack (gitbook.mor.org = ecosystem,
nodedocs.mor.org = technical reference, morlord.com = analytics). This prototype
should **not** duplicate nodedocs' technical content — it's the "how do I start"
layer, journey-driven, screenshot/video-heavy.

**Status:** deployed for internal review at https://morpheus-asia.mintlify.site/ on
Mintlify's free Starter tier, auto-deploying from `master` on every push. This is
**not access-controlled** — Starter has no password/SSO auth, only "Mintlify auth"
(Pro, $450–540/mo, is required for real password protection). The URL is unlisted
and "Don't index project" is enabled in the dashboard (noindex + robots.txt
disallow) so it won't surface via search, but anyone with the link can view it —
share it only directly with staff, never in a public/crawlable channel. This is a
deliberate, time-boxed trade-off for the review stage, not a real access-control
decision. No production domain or deployment decisions have been made — those sit
with US leadership, out of scope for this work. See the research behind this
choice for the full comparison of hosting options and why Vercel/Netlify/Cloudflare
Pages/GitHub Pages aren't viable alternatives (Mintlify has no static-export path
outside an Enterprise agreement).

## Structure

```
docs.json              Mintlify nav config
what-is-morpheus.mdx   Newcomer explainer, linked from index.mdx above the path cards
index.mdx              Landing page: API Gateway vs Native decision, task recipes
native-path/           Wallet Setup, Funding, Desktop App, morctl, Headless/Developer,
                        Provider guides
api-gateway/           Windows, macOS, Ubuntu OpenClaw+EverClaw setup guides, vscode.mdx
```

## Content audit (2026-09-03) and Phase 1 follow-up

A full content-gap audit against apidocs.mor.org and nodedocs.mor.org (83 official
pages reviewed) found the official docs strong on technical depth but with almost no
visuals (zero screenshots across all 63 nodedocs.mor.org pages) and three real content
dead ends: no acquisition/bridging instructions for MOR/ETH on Base anywhere official,
wallet/seed-phrase safety reduced to one sentence per install page, and no unified map
of the ~13 mor.org-ecosystem properties. Phase 1 of the resulting roadmap is done:
`what-is-morpheus.mdx`, `native-path/wallet-setup.mdx`,
`native-path/funding-your-wallet.mdx`, and the converted `api-gateway/vscode.mdx` all
exist and are wired into `docs.json` + cross-linked from `index.mdx`,
`desktop-app.mdx`, `morctl.mdx`, and `provider.mdx`. Remaining phases (session-lifecycle
diagram, ecosystem map page, zero-to-earning provider checklist, onboarding videos) are
not yet started — see the audit artifact from that session for the full prioritized
list if picking this back up.

## Validate before assuming anything works

```bash
npm install -g mintlify   # first time only; use PUPPETEER_SKIP_DOWNLOAD=true if
                           # the puppeteer browser download is blocked by network policy
mintlify broken-links      # catches MDX syntax errors AND broken internal links
mintlify dev                # local preview at localhost:3000
```
`broken-links` is not just for links — it's the fastest way to catch MDX parse
errors before wasting time debugging a broken preview.

## MDX gotchas already hit once — don't reintroduce these

1. **Unfenced JSON/curly-brace content breaks MDX.** MDX parses `{...}` as a JSX
   expression outside of a fenced code block. Any raw JSON snippet (e.g. a config
   example) must be inside triple-backtick fences, not bare text.
2. **Angle-bracket placeholders break MDX.** `<model-name>` as a bare placeholder
   gets read as an unclosed HTML/JSX tag. Wrap any angle-bracket placeholder in
   backticks: `` `<model-name>` ``.
3. **Step sections use Mintlify's native `<Steps>`/`<Step title="...">` component**,
   not `## Step N:` headings as plain text. This is the actual mechanism that gives
   nodedocs.mor.org / apidocs.mor.org their consistent numbered-step look — both
   run on Mintlify, so matching their visual style is "use the platform's built-in
   components correctly," not custom CSS.

## Known content gaps (expected, not bugs)

- **Screenshots are not embedded anywhere yet.** Every `📸 Capture N` / `Screenshot
  required` marker is a text placeholder describing what to capture — no image
  files exist. When adding real screenshots: save under `<section>/images/`, embed
  with `<Frame><img src="..." alt="..." /></Frame>` (matches Mintlify/nodedocs
  convention), then remove the placeholder text.
- **macOS screenshots** and the **Morpheus P2P Inference in OpenClaw — Setup &
  Operations Guide** are known, explicitly-accepted in-progress items — not launch
  blockers for the local prototype.
- **The VS Code / Cline guide has been converted and shipped** as
  `api-gateway/vscode.mdx` (proper frontmatter, `<Steps>`, registered in `docs.json`
  under a new "IDE Integrations" group). The original draft at
  `docs/Using_Morpheus_in_VS_Code.md` is now superseded — treat the `.mdx` version as
  canonical, not the draft.

## Content decisions made deliberately — don't silently "fix" these back

- **EverClaw's installed directory is confirmed `everclaw`**, not
  `everclaw-inference` — settled by the fresh-install test report
  (`docs/testing/OpenClaw_EverClaw_Fresh_Install_Test_Report.md`, §3.2) on OpenClaw
  `2026.7.1-2`. The API Gateway guides state this as confirmed, with a fallback
  `ls` check in case a future version differs — don't revert to presenting it as
  an open ambiguity.
- **The stake-lock/claim behavior** (Native path guides) is real, current platform
  behavior (~24h hold + manual `withdrawUserStakes` call) confirmed via direct
  testing and cross-referenced against upstream issue #827/PR #832 — not a
  documentation error to "simplify away."
- **`morctl` is the recommended CLI**, not the official `mor-cli` (has confirmed,
  unresolved bugs on Windows — issue #792). Any new content should follow this,
  not reintroduce `mor-cli` as a primary recommendation.
- **Model configuration in the API Gateway guides is now two separate
  procedures, not one "declare + set primary" fix** — corrected per the same test
  report (§6.1, §6.2). (1) Switching the *active* model among already-provisioned
  models no longer requires hand-editing `openclaw.json` at all — `openclaw
  models set <model>` or the TUI `/model <model>` both work and persist across
  restarts. (2) *Provisioning* a model that isn't fully set up by default (e.g.
  `deepseek-v4-flash`) still requires two JSON changes — declare it under
  `models.providers.mor-gateway.models`, and add an alias entry under
  `agents.defaults.models` — but no longer involves hand-editing a `primary`
  field. Don't collapse either procedure to fewer steps, and don't conflate the
  two (provisioning vs. activating are different operations).
- **The default model (`mor-gateway/glm-5`) works out of the box** with a funded
  key — the previously-documented "model too expensive for the hosted API
  gateway" failure did not reproduce on re-test (report §6.3). Don't reintroduce
  advice to avoid or switch away from the default for cost reasons.
- **The `gateway.plugins.bonjour` config block is a required removal, not
  optional cleanup** — on OpenClaw `2026.7.1-2` it's a blocking schema-validation
  failure that prevents `openclaw gateway start`/`restart` from succeeding at all
  (report §5). The guides flag this with a `<Warning>`; don't demote it back to a
  routine aside.
- **Fresh-account "insufficient credits" during bootstrap remains unresolved**,
  not fixed — the fresh-install test report used a pre-funded account (§9), so
  the existing workaround is carried over as an unverified suggestion. Don't
  present it as a confirmed fix.

## Workflow going forward

Prefer direct file edits + `git diff` review over regenerating whole files. Run
`mintlify broken-links` after any structural change (new pages, nav changes,
component usage) before considering it done.
