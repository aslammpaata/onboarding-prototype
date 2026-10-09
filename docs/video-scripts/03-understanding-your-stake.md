# Script: Understanding Your Stake — Where Did My MOR Go?

**Target length:** 3.5–4.5 minutes
**Audience:** anyone who's opened a session on the Native path and been alarmed by
their wallet balance
**Companion page:** `native-path/understanding-session-economics.mdx`
**Verified against:** Lumerin Node **v7.14.0** (latest stable, released 2026-10-02).
Two upstream changes shape this script: MorpheusUI's confirm dialog (v7.12/v7.13)
now shows the stake next to the compute cost *before* you lock anything, and the
router gained on-hold listing and claim endpoints in v7.11.0
(`GET /blockchain/stakes/onhold`, `POST /blockchain/stakes/withdraw`). There's still
**no claim button in the app**.
**Goal:** replace anxiety with a mental model, proven on real on-chain data the
viewer can go verify themselves.

This is the cheapest video to produce in the set — no live software to install, just
narrated navigation of the app's confirm screen, Basescan, and tech.mor.org's
session checker. Shoot it in one sitting.

---

## Before recording

- Have a wallet address with real prior session history ready (the addresses used
  in `understanding-session-economics.mdx`'s worked examples are fine to reference
  by their transaction pattern, but confirm you have a live Basescan page to show
  actual similar transactions — don't fabricate on-screen numbers that don't match
  a real, viewable chain).
- Use sessions that **ran their full duration** for the Section 2 close
  transactions. That's the case the guide's on-chain examples show, where close
  returns only a sliver. An early close behaves differently (the unused part returns
  in the close transaction itself), and Section 2 says so in one line, but the
  footage should match the guide.
- **Flag for the guide, not the video:** nodedocs now frames close as "unused stake
  returns immediately, the used portion is held until the end of the UTC day."
  `understanding-session-economics.mdx` frames it from the full-duration case
  ("only the small used-cost portion returns right away"). Both hold up for their
  case, but the guide should state the early-close behavior explicitly so the video
  and the page don't seem to disagree. Reconcile that before embedding this video.
- Confirm on a current MorpheusUI install that the confirm dialog still shows both
  numbers (stake and compute cost), and that there's still no in-app claim control.
  If a claim button has shipped by the time you record, film it in Section 3
  instead of the API/contract options.

---

## Cold Open (0:00–0:20)

**[SCREEN]** Wallet tab, balance visibly dropping after opening a session — held for
a beat, slightly alarming by design.

**[VO]**
> "If you've opened a session on Morpheus and watched way more MOR disappear than
> the price you expected, you're not imagining it, and you didn't lose it. Let's
> look at exactly where it went — with real transaction data, not just a promise."

---

## Section 1: Three Words, Not One (0:20–1:20)

**[TEXT OVERLAY, three terms appearing in sequence]** "Cost. Escrow. Claim."

**[VO]**
> "Three words get conflated here, so let's separate them. **Cost** is what the
> session actually charges you — price per second, times duration. Small. **Escrow**
> is what gets locked when you open the session, to guarantee that cost — and it's
> roughly 338 times larger than the cost itself. **Claim** is a separate action you
> take later to move released escrow back into spendable balance."

**[SCREEN]** MorpheusUI's session confirm dialog. Zoom on the two figures side by
side: the MOR to be locked, and the compute cost.

**[TEXT OVERLAY]** "The app now shows both numbers before you confirm."

**[VO]**
> "And you don't have to take that ratio on faith anymore. Newer versions of the
> desktop app show both numbers on the confirm screen, before anything is locked.
> Here's the stake, and here's the compute cost — a few hundred times apart, exactly
> as the math says."

**[TEXT OVERLAY, optional, small, for the curious]**
"stake = supply × price per second × (duration + 1s) ÷ today's budget"

**[TEXT OVERLAY]** "Opening a session escrows MOR. It does not spend it."

**[VO]**
> "The one sentence that matters most: opening a session escrows MOR, it does not
> spend it. The number that drops in your wallet is the large escrow number, not the
> small cost — and that's exactly why it looks alarming if nobody told you."

---

## Section 2: Watch It Happen, On-Chain (1:20–2:30)

**[SCREEN]** Basescan, wallet address's transaction history, filtered to Diamond
contract interactions.

**[VO]**
> "Here's a real wallet's transaction history. Watch these three sessions."

**[SCREEN]** Highlight/zoom on the Open Session → Close Session pairs, showing the
large escrow amount vs. the tiny returned amount on close.

**[TEXT OVERLAY]** "Escrowed: 1.18 MOR. Returned on close: 0.003 MOR."

**[VO]**
> "These sessions ran their full length. Each Close Session transaction only returns
> a small fraction of a percent of what was escrowed. That's not a glitch — that's
> the small stipend settling immediately. The rest doesn't vanish. It moves somewhere
> else."

**[TEXT OVERLAY]** "Closed early? The unused part of the stake returns in the close
transaction itself."

**[VO]**
> "One thing worth knowing: if you close a session early, the unused part of the
> stake comes back right there in the close transaction. What gets held is the part
> that covered the time you actually used."

---

## Section 3: The On-Hold Queue and Claiming (2:30–3:30)

**[SCREEN]** Cut to the session-lifecycle diagram from `understanding-session-economics.mdx`
(screen-recorded from the live doc page, or rebuilt as a simple animated graphic
matching the same flow).

**[VO]**
> "The rest sits in an on-hold queue — day-locked, not spendable, including as
> escrow for your next session. It's released at the end of the UTC day the session
> ended, and then it needs a claim — a separate transaction called Withdraw User
> Stakes. In practice that's taken anywhere from under an hour to a few days."

**[SCREEN]** tech.mor.org/session.html with the wallet address entered, showing the
three buckets: in your wallet, locked in an active session, and on hold.

**[TEXT OVERLAY]** "tech.mor.org/session.html — read-only checker. It shows where your
MOR is; it doesn't claim it."

**[VO]**
> "If you want to see where your own MOR is sitting right now, tech.mor.org's session
> checker splits it into those three places — your wallet, active sessions, and on
> hold. It's read-only: it shows you, it doesn't claim for you."

**[TEXT OVERLAY]** "No claim button in the desktop app yet. Claim via your node's
API (`POST /blockchain/stakes/withdraw`) or the contract's `withdrawUserStakes`."

**[VO]**
> "There's no claim button in the desktop app yet. You claim either through your
> node's API, which got dedicated endpoints for listing and claiming held stake
> recently, or by calling the contract directly. The written guide has both."

**[SCREEN]** Basescan again, showing a `Withdraw User Stakes` transaction landing.

**[VO]**
> "Either way, this is the transaction you're looking for — the one that actually
> returns the bulk of your MOR to spendable balance."

---

## Section 4: What This Means for You (3:30–4:00)

**[TEXT OVERLAY]** "Budget for your largest single open escrow, not your cumulative
usage."

**[VO]**
> "The practical takeaway: don't budget MOR based on how much inference you think
> you'll use in a day. Budget based on the largest single session's escrow you'll
> have open at once, plus a bit for the next one. Usage cost is small. What actually
> matters is how much capital is tied up in escrow at any given moment."

**[TEXT OVERLAY]** "Verify this yourself: basescan.org/address/YOUR_ADDRESS"

**[VO]**
> "And you don't have to take any of this on faith — your own wallet's transaction
> history on Basescan shows you exactly the same pattern. The written guide linked
> below walks through identifying each transaction type step by step."

---

## Outro (4:00–4:15)

**[SCREEN]** Standard outro card.

**[VO]**
> "That's where your MOR actually goes. Full guide, live calculator link, and
> on-chain verification steps below."

---

## YouTube description

Paste-ready. Replace `[GUIDE_URL]` with the production domain once one is decided —
**never** the `morpheus-asia.mintlify.site` review URL. Re-check chapter times
against the final edit.

```text
Opened a Morpheus session and watched far more MOR leave your wallet than the price
you expected? You didn't lose it. Opening a session locks a refundable stake that's
a few hundred times the actual compute cost. This video shows where that MOR goes,
when you get it back, and how to check every step yourself on-chain.

Recorded against MorpheusUI / Lumerin Node v7.14.0. The desktop app shows the stake
and the compute cost on the confirm screen before you lock anything. There's no
in-app claim button yet. Claim through your node's API or the contract (see the
guide).

Chapters
0:00 "Where did my MOR go?"
0:20 Cost, escrow and claim
1:20 Watching it happen on Basescan
2:30 The on-hold queue and claiming
3:30 How much MOR you actually need
4:00 Links

Links
Understanding session economics, with real transaction data: [GUIDE_URL]/native-path/understanding-session-economics
Check where your MOR is sitting (read-only): https://tech.mor.org/session.html
Stake calculator: https://tech.mor.org/calc.html
Look up your own transactions: https://basescan.org
Questions? Morpheus Discord: https://discord.gg/kyVaxTHnvB
```
