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

**Status:** local/free-hosted prototype only. No production domain or deployment
decisions yet — those sit with US leadership, out of scope for this work.

## Structure

```
docs.json              Mintlify nav config
index.mdx              Landing page: API Gateway vs Native decision, task recipes
native-path/           Desktop App, morctl, Headless/Developer, Provider guides
api-gateway/           Windows, macOS, Ubuntu OpenClaw+EverClaw setup guides
```

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

## Content decisions made deliberately — don't silently "fix" these back

- **EverClaw's installed directory is noted as possibly `everclaw` OR
  `everclaw-inference`** depending on install script/version — this is intentional
  ambiguity pending verification, not an unresolved bug to pick one side of.
- **The stake-lock/claim behavior** (Native path guides) is real, current platform
  behavior (~24h hold + manual `withdrawUserStakes` call) confirmed via direct
  testing and cross-referenced against upstream issue #827/PR #832 — not a
  documentation error to "simplify away."
- **`morctl` is the recommended CLI**, not the official `mor-cli` (has confirmed,
  unresolved bugs on Windows — issue #792). Any new content should follow this,
  not reintroduce `mor-cli` as a primary recommendation.
- **The two-part model-configuration fix** in the API Gateway guides (declare the
  model under `mor-gateway`'s list AND use the provider-qualified `primary` name)
  is deliberately more thorough than earlier drafts — don't collapse it back to a
  single-step instruction.

## Workflow going forward

Prefer direct file edits + `git diff` review over regenerating whole files. Run
`mintlify broken-links` after any structural change (new pages, nav changes,
component usage) before considering it done.
