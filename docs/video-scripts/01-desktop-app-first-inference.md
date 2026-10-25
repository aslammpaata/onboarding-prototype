# Script: Desktop App — First Inference in 5 Minutes

**Target length:** 5–6 minutes
**Audience:** total beginner, no crypto experience assumed
**Companion page:** `native-path/desktop-app.mdx`
**Verified against:** Lumerin Node **v7.17.0** (latest stable, released 2026-10-24) —
screens confirmed hands-on on v7.14.0 (2026-10-25) and re-checked on v7.17.0
(2026-10-26), no differences.
The desktop app's UI was rebuilt in v7.13.0 — it now installs as **MorpheusUI**.
**Goal:** someone with a blank Windows machine gets from zero to a chat reply, and
understands *why* their MOR balance moves the way it does — ideally *before* it
moves, since the app now shows the stake on the confirm screen.

---

## Before recording

- **A clean app state, so the first-launch screens appear.** Sections 1–2 film the
  install, the password screen and **Create**, which only show on a fresh install.
  Your current install already has your old wallet imported, so record on a fresh
  Windows VM or a separate Windows user account. Don't wipe the existing install's
  data to get there unless your old wallet's seed phrase is safely backed up — erasing
  it removes that wallet from the app.
- **The throwaway wallet is created and funded on camera** (Sections 2–3), with cuts
  over the waits — see the editing notes in those sections. Have the funds ready to
  send from somewhere else: **at least 5 MOR** plus a little Base ETH. Never use a
  real/meaningful wallet as the one being created on camera.
- Download the **latest stable** installer (`win-x64-morpheus-app-7.17.0.exe` at time
  of writing) — never a `-test` pre-release, even if it's newer.
- **Confirmed on a live MorpheusUI v7.14.0 install (2026-10-25) and re-checked on
  v7.17.0 (2026-10-26)**, and
  `desktop-app.mdx` updated with fresh screenshots to match:
  - provider picker (price and score) and the Stake / Direct Pay choice — as scripted;
  - confirm dialog showing MOR to be locked next to compute cost — as scripted;
  - per-answer token count and cost sit **directly under each reply**
    (e.g. `5 in · 9 out · <0.0001 MOR`);
  - the account switcher is the **Account** dropdown at the top of the Wallet tab,
    next to the address;
  - **Workspace only becomes available once a session is open**;
  - the session card at the top of Chat shows session ID, **MOR staked** and a
    countdown — not a separate cost figure;
  - **there's no early-close control** in the app. The script now lets the session
    run out and doesn't tell viewers to close early.
  - the Wallet tab's **Staking Dashboard** link (`staking.mor.lumerin.io`) is
    Lumerin's LMR → MOR staking pools, **not** a way to claim session stake — so the
    "no claim button" lines in Section 5 stand. Don't linger on that link on camera;
    viewers may assume it's where they claim.
- **Capture during the shoot:** a fresh seed-phrase screenshot for `desktop-app.mdx`
  (still the v7.3.0 one) — see the editing note in Section 2.

---

## Cold Open (0:00–0:15)

**[SCREEN]** Static title card: "Your First AI Chat on Morpheus — No Crypto
Experience Required"

**[VO]**
> "In the next five minutes, you're going to install an app, chat with an AI model
> running on someone else's computer on the other side of the world, and understand
> exactly what happened to your wallet balance when you did it. Let's go."

---

## Section 1: Download & Install (0:15–0:50)

**[SCREEN]** Browser open to the GitHub Releases page.

**[VO]**
> "Head to the Morpheus release page — link in the description — and download the
> Windows installer from the latest release. The file name looks like
> `win-x64-morpheus-app`, followed by a version number. Skip anything tagged 'test'
> — those are pre-releases."

**[TEXT OVERLAY]** "Mac: `mac-arm64` (Apple Silicon) or `mac-x64` (Intel) `.dmg` ·
Linux: `.AppImage`"

**[SCREEN]** Run the installer. Windows SmartScreen warning appears.

**[TEXT OVERLAY]** "Expected — click More info → Run anyway"

**[VO]**
> "Windows will probably throw up a SmartScreen warning here. That's expected for a
> new installer — click 'More info,' then 'Run anyway.'"

**[SCREEN]** Installation completes; launch **MorpheusUI** from the Start menu.

**[VO]**
> "Once it's installed, it shows up as MorpheusUI — that's the desktop app."

---

## Section 2: Wallet Setup (0:50–2:00)

**[SCREEN]** First-launch screen, set password.

**[VO]**
> "First launch asks you to set a password. This password only unlocks the app on
> *this* device — keep that in mind, because it matters for what's next."

**[SCREEN]** Choose "Create."

**[VO]**
> "Since this is a new wallet, choose Create."

**[SCREEN]** Seed phrase screen appears.

**[TEXT OVERLAY, large, held for full duration of this beat]**
> "STOP. Write this down on paper. Never screenshot it. Never share it. Nobody
> legitimate will ever ask you for it."

**[EDITING NOTE: cut to a static graphic or blurred mock screen here — do not show a
real generated seed phrase on camera, even a throwaway one.]**

**[CAPTURE NOTE: while this screen is up, take a still for the guide (it replaces
`native-path/images/desktop-app-01-seed-phrase.png`). Blur every word before it
leaves your machine — the saved file should never contain a readable phrase.]**

**[VO, slower, deliberate pace]**
> "This is your seed phrase. It is the *only* backup for this wallet — there's no
> password reset, no support ticket that gets it back for you. Whoever has these
> words has full control of whatever's in this wallet, forever. Write them on paper,
> put the paper somewhere safe, and never type them into anything ever again."

**[TEXT OVERLAY]** "Recovering an existing wallet and it shows 0 MOR? Check the
Account dropdown at the top of the Wallet tab — your funds may be on another account."

**[VO]**
> "Quick tip if you chose Recover instead: one seed phrase can hold several accounts.
> If your balance shows zero after recovering, open the Account dropdown at the top
> of the Wallet tab, next to your address, before you assume anything's wrong — the
> funds are often just on another one."

---

## Section 3: Funding (2:00–2:40)

**[SCREEN]** Wallet tab, showing `0 MOR / 0 ETH`.

**[VO]**
> "Your wallet exists now, but it's empty — that's expected. You need two things to
> use a paid model: a small amount of ETH on the Base network, which pays for the
> tiny transaction fees, and MOR, which is what you're actually here for."

**[TEXT OVERLAY]** Simple two-row table: "ETH (Base) → the toll for the road" /
"MOR → what you're here for"

**[SCREEN]** Copy the address (or open Receive's QR code), then start the transfer of
MOR and Base ETH from the funding wallet.

**[EDITING NOTE: cut from the transfer starting to the Wallet tab showing the funded
balance — skip the wait for confirmation. A quick "a few moments later" card is
enough. Don't show the funding wallet's own balance or seed phrase on screen.]**

**[TEXT OVERLAY]** "You need at least 5 MOR to open a session, plus a little Base ETH."

**[VO]**
> "Send both to the address shown, or use the QR code if you're funding from a
> phone wallet. You'll need at least five MOR to open a session, and only a little
> ETH — fees on Base are tiny."

**[TEXT OVERLAY]** "Don't want to deal with crypto yet? Skip this — pick the free
bundled local model instead."

**[VO]**
> "If you'd rather skip all of that for now, Morpheus ships with a small local model
> that runs entirely on your machine, completely free. We'll use a paid network
> model for this walkthrough so you can see the full picture, but that free option
> is always there."

---

## Section 4: Choosing a Model & Opening a Session (2:40–4:10)

**[SCREEN]** Chat → Change Model. Search or sort the list by price per second; pick
any model other than "Local Model."

**[VO]**
> "In Chat, click Change Model. You can search the list or sort it by price per
> second. Any model without a live provider right now shows a note instead of a
> price — just pick one that has a price."

**[SCREEN]** Provider picker — each provider's price and score, top-scored one
selected by default. Payment left on **Stake**.

**[VO]**
> "Next you'll see the providers offering that model, each with a price and a score.
> The top-scored one is already selected, and that's a sensible default. Leave the
> payment option on Stake — it locks the same amount either way."

**[SCREEN]** Confirm dialog — hold on it. Zoom on the two numbers: MOR to be locked,
and compute cost.

**[TEXT OVERLAY]** "Big number = refundable stake (locked, not spent). Small number
= what the session actually costs."

**[VO]**
> "This screen is where people used to get surprised — so read it before you click.
> There are two numbers, and they're a few hundred times apart. The big one is your
> stake: a refundable deposit, like a hotel security deposit. It's locked to
> guarantee the session, not spent. The small one is the compute cost — what you're
> actually paying."

**[SCREEN]** Confirm. Cut to the Wallet tab — available MOR drops by the stake
amount and Staked Balance shows it, as the dialog said it would.

**[VO]**
> "Confirm, and the wallet does exactly what that screen told you: available MOR
> drops by the stake amount, and it shows up here as your staked balance. No
> surprise, no lost money."

**[SCREEN]** Back to Chat — the session card at the top: session ID, MOR staked,
countdown timer. Then the Workspace button lighting up.

**[VO]**
> "Up top you've now got your session: its ID, the stake you just locked, and how
> long it has left. Workspace — the mode for multi-step tasks — also unlocks now,
> because it only works while a session is open."

**[TEXT OVERLAY, small]** "Clicked Open twice? The app reuses the session — it
won't lock your stake twice."

---

## Section 5: Chat & What Happens After (4:10–5:20)

**[SCREEN]** Type a message, get a response.

**[VO]**
> "And that's it — that response was generated on hardware you don't own, run by
> someone you've never met, coordinated entirely by code. That's decentralized AI
> inference."

**[SCREEN]** Zoom on the line under the reply: `5 in · 9 out · <0.0001 MOR`.

**[VO]**
> "Right under each answer you get its token count and cost — this one cost less
> than a ten-thousandth of a MOR. That's the compute cost at work, and you can see
> how small it is next to the stake."

**[SCREEN]** Let the session timer run out (sped up in editing), then cut to the
Wallet tab post-close.

**[VO]**
> "One more thing worth knowing before you go. The app doesn't have a button to end
> a session early — it runs until the timer hits zero. And because it ran its full
> time, only a sliver of the stake comes straight back. The rest is held until the
> end of that day, UTC — so up to about 24 hours — and then it needs a separate
> claim to land back in your spendable balance. That's normal, current network
> behavior — not a bug, and not money lost."

**[TEXT OVERLAY]** "No claim button in the app yet — the guide shows how to claim."

**[VO]**
> "There's no claim button in the app yet, so the written guide below shows you
> exactly how to claim."

**[TEXT OVERLAY]** "Full breakdown, with real on-chain numbers: [link to
Understanding Session Economics]"

**[VO]**
> "If you want to see that proven with real transaction data instead of just taking
> my word for it, there's a link below to a page that walks through it on-chain,
> step by step."

---

## Outro (5:20–5:35)

**[SCREEN]** Standard outro card.

**[VO]**
> "That's your first inference on Morpheus. Full written guide, troubleshooting
> table, and next steps are linked below — see you in the next one."

---

## YouTube description

Paste-ready. Replace `[GUIDE_URL]` with the production domain once one is decided —
**never** the `morpheus-asia.mintlify.site` review URL (see `CLAUDE.md`: share that
only directly with staff, never in a public channel). Re-check chapter times against
the final edit.

```text
Install the Morpheus desktop app (MorpheusUI), set up a wallet, and chat with an AI
model running on someone else's hardware, in about five minutes. No crypto
experience needed. We also cover what happens to your MOR when you open a session:
it's a refundable stake, not a payment, and the app now shows you both numbers
before you confirm.

Recorded on MorpheusUI / Lumerin Node v7.17.0. If you're on a newer version, some
screens may look different. The written guide is kept up to date.

Chapters
0:00 What you'll do
0:15 Download and install
0:50 Wallet setup and your seed phrase
2:00 Funding: MOR and ETH on Base
2:40 Picking a model and provider, opening a session
4:10 Chatting, and what happens when the session ends
5:20 Next steps

Links
Written walkthrough: [GUIDE_URL]/native-path/desktop-app
Wallet setup and seed phrase safety: [GUIDE_URL]/native-path/wallet-setup
Getting MOR and ETH on Base: [GUIDE_URL]/native-path/funding-your-wallet
Where your MOR goes, with real on-chain numbers: [GUIDE_URL]/native-path/understanding-session-economics
Desktop app downloads (pick the latest release, not a "test" one): https://github.com/MorpheusAIs/Morpheus-Lumerin-Node/releases
Questions? Morpheus Discord: https://discord.gg/kyVaxTHnvB

Nobody from Morpheus will ever ask for your seed phrase. Anyone who does is trying
to steal your wallet.
```
