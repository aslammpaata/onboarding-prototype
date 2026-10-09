# Script: OpenClaw + EverClaw Setup, Start to First Response

**Target length:** 8–10 minutes
**Audience:** developer/technical, wants an AI agent using Morpheus inference, no
wallet or node
**Companion page:** `api-gateway/windows.mdx` (Windows + WSL2 path — most-documented
internally; a macOS or native-Ubuntu variant can reuse this script's structure with
the platform-specific prerequisite section swapped out)
**Goal:** viewer ends with a working OpenClaw + EverClaw setup talking to Morpheus,
having personally seen the one confirmed blocking bug (the bonjour plugin) and its
fix, so they're not blindsided by it.

---

## Before recording

- Fresh Windows 10/11 machine or VM, WSL2 **not** yet installed (or record the reboot
  separately and edit the cut in — a live reboot mid-recording is a real risk).
- A funded `sk-...` API key ready at `app.mor.org` (create it on camera, or have it
  ready and just show the key format).
- Re-confirm against the current fresh-install test report that the bonjour-plugin
  failure and the `everclaw` directory name are still accurate before recording —
  these are the two facts in this script most likely to drift with a platform
  update.

---

## Cold Open (0:00–0:20)

**[SCREEN]** Title card: "Connect Your AI Agent to Morpheus — No Wallet Required"

**[VO]**
> "This is the fastest way to get an AI agent talking to Morpheus's decentralized
> inference network — no wallet, no staking, no local node. Just an API key and two
> command-line tools. There's one real bug we'll hit along the way, and I'll show
> you the fix live rather than editing around it, because you'll almost certainly
> hit it too."

---

## Section 1: Get Your API Key (0:20–1:00)

**[SCREEN]** Browser: `app.mor.org` → sign in → Inference API Admin page → create
key.

**[VO]**
> "First, grab a Morpheus API key at app.mor.org. Sign in, go to the Inference API
> admin page, and create a key — it starts with `sk-`. Copy it now, because it's
> only shown once."

**[TEXT OVERLAY]** "sk-... = your real API key. evcl-... = a different, temporary
bootstrap key you'll see later. Don't mix them up."

---

## Section 2: WSL2 Prerequisites (1:00–2:30)

**[SCREEN]** PowerShell as Administrator: `wsl --install`

**[VO]**
> "EverClaw only runs on macOS, Linux, or WSL2 — not native PowerShell or Command
> Prompt. So on Windows, step one is WSL2."

**[SCREEN]** Reboot (cut here in editing if recorded separately), then WSL2 Ubuntu
first-launch — create a Linux username and password.

**[SCREEN]** `sudo apt update && sudo apt upgrade -y`

**[VO]**
> "After the reboot, WSL2 opens an Ubuntu terminal for the first time — set a Linux
> username and password, then update packages before doing anything else."

---

## Section 3: Install OpenClaw (2:30–4:30)

**[SCREEN]** Terminal: `curl -fsSL https://openclaw.ai/install.sh | bash`

**[VO]**
> "One command installs OpenClaw. It'll walk you through a setup wizard — here's
> exactly what to answer at each prompt."

**[SCREEN]** Step through the wizard, showing each prompt and the answer, with
on-screen captions matching each choice as it's selected:

**[TEXT OVERLAY per prompt, timed to each screen]**
- "Continue setup? → Yes"
- "Setup mode → QuickStart"
- "Model/auth → Skip for now"
- "Keep current model → Yes"
- "Channel → Skip for now"
- "Search provider → Skip for now"
- "Configure skills? → No"
- "Hooks → enable all"
- "Hatch → later"

**[VO]**
> "You'll configure the actual model connection through EverClaw in a moment, so
> most of these can be skipped for now — that's expected, not a mistake."

---

## Section 4: Install EverClaw (4:30–5:30)

**[SCREEN]** Terminal: `curl -fsSL https://get.everclaw.xyz | bash` (or the GitHub
raw install script, per the guide).

**[VO]**
> "Next, EverClaw — the skill that actually connects OpenClaw to Morpheus. This
> installer also sets up a temporary bootstrap key and a local fallback model, so
> you're never fully stuck even before adding your own key."

**[SCREEN]** Installer output completing.

---

## Section 5: Import Your API Key (5:30–6:30)

**[SCREEN]** `cd ~/.openclaw/workspace/skills/everclaw`

**[TEXT OVERLAY]** "Directory is named `everclaw` — if a future version differs, `ls`
the skills folder to confirm."

**[SCREEN]** `npm run bootstrap -- --key sk-YOUR_KEY`

**[VO]**
> "This is where your real `sk-` key replaces the temporary bootstrap key EverClaw
> installed for you."

---

## Section 6: The Bonjour Fix (6:30–7:45) — the important part

**[SCREEN]** Run `openclaw gateway start` — it fails with a schema-validation error.

**[TEXT OVERLAY, held]** "This is a known, confirmed issue — not something you did
wrong."

**[VO]**
> "This failure is real, confirmed, and — as of this recording — still present. The
> gateway won't start because of one config block. Here's the fix."

**[SCREEN]** `nano ~/.openclaw/openclaw.json`, scroll to and delete the
`plugins.bonjour` block, save (Ctrl+X, Y, Enter).

**[SCREEN]** `openclaw config validate` → passes. `openclaw gateway restart` →
succeeds.

**[VO]**
> "Delete that block, validate the config, restart the gateway — and it comes up
> clean."

---

## Section 7: First Chat (7:45–8:30)

**[SCREEN]** `openclaw chat`, type a message, get a response.

**[VO]**
> "And that's a live response, routed through the Morpheus API Gateway."

**[TEXT OVERLAY]** "Want to run a diagnostic instead of taking my word for it?
`bash ~/.openclaw/workspace/skills/everclaw/scripts/diagnose.sh`"

**[VO]**
> "If you want to double-check everything's wired up correctly, EverClaw ships a
> diagnostic script — a few of its checks are expected to fail in Gateway-only mode,
> that's normal, the one that matters is the Morpheus API Gateway reachability
> check."

---

## Outro (8:30–9:00)

**[SCREEN]** Standard outro card.

**[VO]**
> "Full written guide — including model switching, troubleshooting, and the macOS
> and native-Ubuntu versions of this setup — is linked below. See you in the next
> one."

---

## Notes for macOS / native-Ubuntu variants

Skip Section 2 (WSL2) entirely:
- **macOS:** verify Node.js + Git are installed, otherwise identical from Section 3
  onward.
- **Ubuntu (VirtualBox VM):** replace Section 2 with VM creation (ISO download,
  VirtualBox install, VM setup with ≥4GB RAM / 2 CPUs, Guest Additions), then
  identical from Section 3 onward.
