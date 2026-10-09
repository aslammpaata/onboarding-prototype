# Video Production Notes

Covers all four scripts in this folder. Read this before recording any of them.

## Platform baseline

Scripts 01, 03, and 04 were last updated on **2026-10-09** against **Lumerin Node
v7.14.0**, the latest stable release (2026-10-02). Record only on a stable release,
never a `-test` pre-release, even when the pre-release is newer: as of this update
`v7.14.43-test` exists, but it's a pre-release.

Upstream changes since the scripts were first written, and what they changed:

| Release | Change | Scripts affected |
|---|---|---|
| v7.11.0 | Router endpoints to list and claim held stake (`GET /blockchain/stakes/onhold`, `POST /blockchain/stakes/withdraw`). No claim button in the app. | 01, 03 |
| v7.12.0 | `POST /config/models/reload`: reload `models-config.json` without a restart. | 04 |
| v7.12 / v7.13.0 | Desktop app rebuilt and renamed **MorpheusUI**. Adds a provider picker (price and score), a confirm dialog showing the stake next to the compute cost, double-click protection on session open, per-answer token count and cost, a multi-wallet switcher, and HD account switching. | 01, 03 |
| v7.13.0 | New stake formula (`ceil(supply × pricePerSecond × (duration + 1) ÷ todaysBudget)`). Admin API CORS now defaults to loopback, Electron, and `myprovider.mor.org`. | 03, 04 |
| v7.14.0 | Session and audio security hardening. Nothing visible in these scripts. | none |

**Script 02 (OpenClaw + EverClaw) is unaffected:** the API Gateway path doesn't run
the proxy router. Re-check it against OpenClaw/EverClaw releases instead (see
Accuracy guardrails).

**The written guides lag these scripts in three places.** Fix them before recording
the matching video, so the video and the page it embeds on agree:

- `native-path/desktop-app.mdx` is still `last_verified: v7.3.0`, from before the
  MorpheusUI rebuild. Its model, session, and wallet steps describe the old screens.
- `native-path/provider.mdx` says to unzip a router `.zip`, but releases now publish a
  plain binary. It also doesn't mention `POST /config/models/reload` yet.
- `native-path/understanding-session-economics.mdx` explains close-time refunds only
  for sessions that run their full duration. It should also say that an early close
  returns the unused stake straight away, as nodedocs now does.
- (Not tied to a video: `native-path/headless-developer.mdx` still pins v7.3.0
  download URLs.)

## Priority order

1. **Desktop App: First Inference in 5 Minutes** — shortest to produce, unblocks the
   single largest audience (total beginners), no live software risk beyond the app
   itself.
2. **Understanding Your Stake: Where Did My MOR Go?** — short, no new install to
   demo, just narrated Basescan navigation. High value-to-effort ratio.
3. **OpenClaw + EverClaw Setup, Start to First Response** — longer and higher-risk to
   record (live bonjour-plugin fix, WSL2 install, a reboot mid-recording). Do this
   once you've got a comfortable recording rhythm from #1 and #2.
4. **Becoming a Provider: Zero to First Payout** — most complex, smallest audience.
   Last, and only after the Provider guide's two remaining screenshot placeholders
   are filled (see `native-path/provider.mdx`) so the video and written guide stay in
   sync.

## Recording setup

- **Screen recording:** OBS Studio (free, cross-platform) at 1080p/30fps minimum.
  Record system audio separately muted — narrate in post, don't rely on live mic
  commentary. This lets you re-take narration without re-doing the screen capture.
- **Narration:** a real human voice reading the script beats TTS for this content —
  the material is trust-sensitive (wallets, seed phrases, money), and a synthetic
  voice reads as lower-effort exactly where viewers need to trust the source most.
- **Captions:** burned-in captions on every video, no exceptions. A large share of
  Discord/social viewing happens muted. Also export an `.srt` alongside each upload
  for accessibility and for embedding transcripts on the guide page itself.
- **Branding:** 3-second cold open — MorpheusGuide logo on the site's primary green
  (`#16A34A`), no music sting needed. Same outro card on every video: logo + the
  specific page URL the video accompanies (e.g. `[GUIDE_URL]/native-path/desktop-app`)
  + "Questions? Discord" line. `[GUIDE_URL]` is the production domain, which hasn't
  been decided yet (`onboarding.mor.org` / `guides.mor.org` are candidates, not
  decisions). Don't burn a guessed domain into a video, and never use the
  `morpheus-asia.mintlify.site` review URL on screen. Per `CLAUDE.md`, that link is
  shared only directly with staff.
- **On-screen version:** say or show the version you recorded on (e.g. "MorpheusUI
  v7.14.0") once, early. When the UI changes again, viewers can tell the video is
  older than their app instead of assuming they did something wrong.

## Video descriptions

Scripts 01, 03, and 04 each end with a paste-ready **YouTube description**: summary,
recorded-on version, chapter timestamps, and links. Before publishing:

- Replace every `[GUIDE_URL]` with the production domain. If you upload before a
  domain is decided, leave the guide links out rather than use the review URL.
- Re-time the chapters against the final edit. YouTube needs the first chapter at
  `0:00` and at least three chapters, each 10 seconds or longer.
- Keep the "Recorded on vX.Y.Z" line accurate. It's the cheapest way to keep an old
  video from misleading people.

Script 02 doesn't have a description yet. It can follow the same format.

## Test data, not real data

Every recording uses a **fresh, throwaway wallet funded with a small amount** (at
least 5 MOR, the minimum to open a session, plus enough ETH for gas) — never a wallet
with meaningful funds, and never a real
seed phrase captured on screen even blurred. When Video 1 reaches the seed-phrase
screen, cut to a static graphic or blur overlay rather than showing real generated
words — this avoids "was that footage's phrase actually used" questions forever, for
a trivial editing cost.

## Hosting and embedding

Follow the pattern apidocs.mor.org already uses for its OpenCode and Open Web-UI
integration pages: upload to YouTube (unlisted is fine while the site itself is
unlisted — see `CLAUDE.md`'s deployment note), then embed with a standard responsive
iframe directly in the relevant `.mdx` page:

```html
<iframe
  width="560"
  height="315"
  src="https://www.youtube.com/embed/VIDEO_ID"
  title="Video title"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen
></iframe>
```

Do **not** add a placeholder iframe or a "video coming soon" marker to the guide
pages before a video actually exists — this repo already hit exactly that problem
once with screenshot placeholder text (see `CLAUDE.md`'s known-gaps section) and it's
not worth reintroducing. Add the embed only once `VIDEO_ID` is real.

**Suggested embed locations**, once produced:
| Video | Target page | Placement |
|---|---|---|
| Desktop App | `native-path/desktop-app.mdx` | Top of page, before "Walkthrough" |
| OpenClaw + EverClaw | `api-gateway/windows.mdx` (and cross-linked from macos/ubuntu) | Top of page |
| Understanding Your Stake | `native-path/understanding-session-economics.mdx` | Directly under the new session-lifecycle diagram |
| Provider: Zero to Payout | `native-path/provider.mdx` | Top of page, before "Walkthrough" |

## Accuracy guardrails

Every script below is written directly from the current guide content, not from
memory of the product. Before recording, diff the script's factual claims against
the live `.mdx` file it's based on — guides get corrected over time (see
`CLAUDE.md`'s "Content decisions made deliberately" section) and a video is much
more expensive to re-record than a paragraph is to re-edit. Before each recording
session, re-check:

- the [releases page](https://github.com/MorpheusAIs/Morpheus-Lumerin-Node/releases)
  for a stable release newer than v7.14.0. If there is one, skim its notes for
  MorpheusUI, session, or stake changes before shooting 01, 03, or 04;
- the stake hold: held until the end of the UTC day the session ended (up to about
  24 hours), then a manual claim;
- that MorpheusUI still has **no in-app claim button**. If one has shipped, film it;
- the 5 MOR minimum to open a session;
- for 02: the bonjour-plugin fix is still required, and the EverClaw directory is
  still `everclaw`.
