# Script: Desktop App — First Inference in 5 Minutes

**Target length:** 5–6 minutes
**Audience:** total beginner, no crypto experience assumed
**Companion page:** `native-path/desktop-app.mdx`
**Verified against:** Lumerin Node **v7.14.0** (latest stable, released 2026-10-02).
The desktop app's UI was rebuilt in v7.13.0 — it now installs as **MorpheusUI**.
**Goal:** someone with a blank Windows machine gets from zero to a chat reply, and
understands *why* their MOR balance moves the way it does — ideally *before* it
moves, since the app now shows the stake on the confirm screen.

---

## Before recording

- Fresh Windows VM or clean user account (no prior Morpheus install).
- A throwaway wallet ready to fund with **at least 5 MOR** (the minimum to open a
  session) plus a small amount of Base ETH (see production notes: never a
  real/meaningful wallet).
- Download the **latest stable** installer (`win-x64-morpheus-app-7.14.0.exe` at time
  of writing) — never a `-test` pre-release, even if it's newer.
- **The written guide lags this script.** `desktop-app.mdx` is still tagged
  `last_verified: v7.3.0`, from before the v7.13 UI rebuild. Update it first (or at
  least in the same pass) so the video and the page it embeds on show the same
  screens.
- Confirm these on the live app before shooting, since they come from release notes
  and the official docs rather than a hands-on capture — adjust the narration to
  the real on-screen labels:
  - the provider picker (price and score per provider, top-scored selected by
    default) and the **Stake / Direct Pay** choice;
  - the confirm dialog showing **MOR to be locked** next to the **compute cost**;
  - where per-answer token count and cost appear in chat;
  - the Wallet tab's account switcher (used in the Recover tip in Section 2);
  - the early-close control (official docs: the time icon next to the model line,
    then **X** next to the session).

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

**[VO, slower, deliberate pace]**
> "This is your seed phrase. It is the *only* backup for this wallet — there's no
> password reset, no support ticket that gets it back for you. Whoever has these
> words has full control of whatever's in this wallet, forever. Write them on paper,
> put the paper somewhere safe, and never type them into anything ever again."

**[TEXT OVERLAY]** "Recovering an existing wallet and it shows 0 MOR? Check the
account switcher in the Wallet tab — your funds may be on a different account."

**[VO]**
> "Quick tip if you chose Recover instead: one seed phrase can hold several accounts.
> If your balance shows zero after recovering, switch accounts in the Wallet tab
> before you assume anything's wrong — the funds are often just on another one."

---

## Section 3: Funding (2:00–2:40)

**[SCREEN]** Wallet tab, showing `0 MOR / 0 ETH`.

**[VO]**
> "Your wallet exists now, but it's empty — that's expected. You need two things to
> use a paid model: a small amount of ETH on the Base network, which pays for the
> tiny transaction fees, and MOR, which is what you're actually here for."

**[TEXT OVERLAY]** Simple two-row table: "ETH (Base) → the toll for the road" /
"MOR → what you're here for"

**[SCREEN]** Show sending funds to the wallet (or the Receive QR code).

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
amount, as the dialog said it would.

**[VO]**
> "Confirm, and the wallet does exactly what that screen told you: available MOR
> drops by the stake amount. No surprise, no lost money."

**[TEXT OVERLAY, small]** "Clicked Open twice? The app reuses the session — it
won't lock your stake twice."

---

## Section 5: Chat & What Happens After (4:10–5:20)

**[SCREEN]** Type a message, get a response.

**[VO]**
> "And that's it — that response was generated on hardware you don't own, run by
> someone you've never met, coordinated entirely by code. That's decentralized AI
> inference."

**[SCREEN]** Zoom on the answer's token count and cost.

**[VO]**
> "Each answer shows its own token count and cost, so you can see what every reply
> actually costs as you go."

**[SCREEN]** Let the session timer run out (sped up in editing), then cut to the
Wallet tab post-close.

**[VO]**
> "One more thing worth knowing before you go. We let this session run to the end,
> and when a session runs its full time, the stake doesn't come straight back. It's
> held until at least the end of that day, UTC — so up to about 24 hours — and then
> it needs a separate claim to land back in your spendable balance. That's normal,
> current network behavior — not a bug, and not money lost."

**[TEXT OVERLAY]** "Closing early? Unused stake comes back right away. There's no
claim button in the app yet — the guide shows how to claim."

**[VO]**
> "If you close a session early instead, the unused part of the stake comes back
> right away. And there's no claim button in the app yet — the written guide below
> shows you exactly how to claim."

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

Recorded on MorpheusUI / Lumerin Node v7.14.0. If you're on a newer version, some
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
