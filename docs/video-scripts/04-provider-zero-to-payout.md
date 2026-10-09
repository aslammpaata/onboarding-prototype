# Script: Becoming a Provider — Zero to First Payout

**Target length:** 12–15 minutes (consider splitting into two parts: Setup, and
Registration/Verification)
**Audience:** developer/technical operator with spare compute, comfortable with a
terminal
**Companion page:** `native-path/provider.mdx`
**Verified against:** Lumerin Node **v7.14.0** (latest stable, released 2026-10-02).
Changes since the guide was written that affect this script: the router ships as a
plain per-platform binary (`linux-x86_64-morpheus-router-<version>`); v7.12.0 added
`POST /config/models/reload` so a `models-config.json` change doesn't need a restart;
v7.13.0 narrowed the admin API's default CORS origins to loopback, Electron, and
`https://myprovider.mor.org`.
**Goal:** viewer takes a machine from nothing to a registered, earning provider, and
knows how to verify they actually got paid — since MyProvider's GUI has no earnings
dashboard yet, on-chain verification is the only reliable way to check.

This is the longest and most technically involved script in the set. Do it last,
after the presenter is comfortable with the format from the first three, and after
`provider.mdx`'s two remaining screenshot placeholders are filled so the video and
written guide show the same UI state.

---

## Before recording

- A real Linux machine or VM with a GPU (or CPU-only for a smaller model) available
  to actually serve inference — this can't be faked with a mockup, viewers will be
  running the same commands live.
- A throwaway wallet, funded with enough Base ETH and MOR to cover provider stake +
  gas (current minimums: see `native-path/provider.mdx`'s cost table — re-verify
  before recording, these figures have shifted before).
- Decide native Linux vs. Windows+WSL2 topology up front — script below assumes
  native Linux for simplicity; note the WSL2 divergence points inline.
- Use the **latest stable** router from the releases page
  (`linux-x86_64-morpheus-router-7.14.0` at time of writing), never a `-test`
  pre-release.
- **Reconcile the guide first:** `provider.mdx`'s router step says
  `unzip linux-x86_64-morpheus-router-*.zip`, but current releases publish the router
  as a plain binary with no zip. Fix the guide (download → `chmod +x`) before
  recording so viewers following both don't hit a mismatch. (`headless-developer.mdx`
  also still pins v7.3.0 download URLs — same cleanup pass.)
- If you demo `POST /config/models/reload`, confirm the admin user you're calling it
  with has the `system_config` permission; otherwise show a router restart instead.
- If you open MyProvider (`myprovider.mor.org`) in a browser against your node, it
  works with the default CORS settings. Any *other* browser-based tool calling the
  admin API needs `CORS_ALLOWED_ORIGINS` set — not needed for the curl-based flow
  below.

---

## Cold Open (0:00–0:25)

**[SCREEN]** Title card: "Turn Spare Compute Into MOR — Provider Setup, Start to
Finish"

**[VO]**
> "If you've got a machine sitting idle — even a modest GPU — this is how you turn
> it into a Morpheus provider that earns MOR for serving AI inference. This one has
> more moving parts than our other guides, so we're going step by step, including
> the mistakes people actually hit."

---

## Section 1: Fund a Wallet on Base (0:25–1:30)

**[SCREEN]** MetaMask, add Base network manually (RPC, chain ID 8453, explorer).

**[VO]**
> "Same starting point as any Native path setup: a wallet funded on Base. If you
> haven't done this before, the linked Wallet Setup and Getting MOR & ETH on Base
> guides cover it in full — we'll move quickly here."

**[SCREEN]** Send Base ETH and MOR to the wallet, confirm balances.

---

## Section 2: Get the Router (1:30–2:15)

**[SCREEN]** Releases page → latest stable release → download
`linux-x86_64-morpheus-router-<version>` (use `linux-arm64-…` on ARM). Then
`chmod +x` it. Mention the build-from-source and Desktop App alternatives verbally
without demoing all three.

**[TEXT OVERLAY]** "Latest stable release only — skip anything tagged `-test`. The
router is a single file: download, `chmod +x`, done."

**[VO]**
> "There are three ways to get the router — a prebuilt binary, building from source,
> or the desktop app. We're using the prebuilt binary because it's the fastest to
> show. Grab the router file for your platform from the latest stable release — not
> one tagged 'test' — and make it executable. It's a single file, nothing to unzip."

---

## Section 3: Choose Your Topology (2:15–3:00)

**[SCREEN]** Simple graphic: native Linux (router and model server on one machine)
vs. Windows+WSL2 (router in WSL2, model server wherever, portproxy bridging them).

**[VO]**
> "If you're on native Linux, your router and model server usually live on the same
> box, and networking is simple. If you're on Windows with WSL2, there's an extra
> bridging step — WSL2's IP address isn't permanent, which trips people up after a
> reboot. We're demonstrating native Linux; the written guide has the full WSL2
> portproxy walkthrough if that's your setup."

---

## Section 4: Stand Up a Model Server (3:00–5:00)

**[SCREEN]** `ollama pull <model>`, then `ollama serve`.

**[VO]**
> "Before touching Morpheus at all, get a model actually serving locally — Ollama is
> the fastest path for this demo. vLLM is the other supported option if you need
> higher throughput; the written guide has its full flag set."

**[SCREEN]** Test the model server directly (curl to its local port) — confirm a
real response, independent of Morpheus.

**[TEXT OVERLAY]** "Confirm this works BEFORE wiring up Morpheus — it's the
foundation everything else depends on."

**[VO]**
> "This step matters more than it looks — if inference doesn't work locally first,
> nothing downstream will, and you'll waste time debugging the wrong layer."

---

## Section 5: Configure the Router (5:00–7:00)

**[SCREEN]** Create `.env` file with the required variables (masked/dummy private
key value shown, never a real one on screen).

**[TEXT OVERLAY]** "Never put a real private key in a file you might screen-share or
commit. Use your OS keychain instead — shown next."

**[SCREEN]** `secret-tool store` (Linux) or `security add-generic-password` (macOS)
to store the wallet private key in the OS keychain rather than the `.env` file.

**[VO]**
> "Store your private key in your operating system's keychain, not in a plaintext
> config file. This is the same principle as never screenshotting a seed phrase —
> the file itself needs to stay out of anything that gets shared or backed up
> insecurely."

**[SCREEN]** Edit `models-config.json` to point at the local model server.

**[TEXT OVERLAY]** "Changed `models-config.json` later? `POST /config/models/reload`
picks it up without a restart (router v7.12+)."

**[VO]**
> "And if you change this file later — say, to add a second model — newer routers can
> reload it without a restart. The exact call is in the description."

---

## Section 6: Register On-Chain (7:00–9:30)

**[SCREEN]** Choose one path to demonstrate — CLI/API via curl is most instructive
on camera (mention the web portal and direct-contract alternatives verbally).

**[TEXT OVERLAY]** "The 0.3 MOR bid fee is non-refundable — the most common way
people lose a small amount of MOR during setup. Double-check the model ID before
submitting."

**[SCREEN]** `POST /blockchain/approve`, then `createBlockchainProvider`, then
`createBlockchainProviderBid`, showing each response.

**[VO]**
> "Three calls: approve the router to spend your MOR, register as a provider, then
> place a bid on the model you're serving. That bid fee is real and non-refundable,
> so this is worth double-checking before you submit, not after."

---

## Section 7: Open the Firewall & Verify Reachability (9:30–11:00)

**[SCREEN]** `ufw allow 3333/tcp` (native Linux).

**[VO]**
> "Your router needs to be reachable from outside your network for other peers to
> route sessions to you. Open the port—"

**[SCREEN]** From a second machine or an external tool, `nc -zv <public-ip> 3333` or
equivalent — confirm external reachability.

**[TEXT OVERLAY]** "[Screenshot/clip: successful external connection test]"

**[VO]**
> "—and confirm from *outside* your own network that it's actually reachable. A lot
> of setup problems turn out to be exactly this step skipped."

---

## Section 8: Start, Verify, and Test Real Inference (11:00–13:00)

**[SCREEN]** Start the router as a systemd service (or foreground for the demo).
Check `/healthcheck` and `/v1/models`.

**[VO]**
> "Once it's running, two quick checks: the healthcheck endpoint, and the models
> endpoint, to confirm your model shows up with a healthy status rather than
> `no_bid` or `local-only`."

**[SCREEN]** Open a session against your own provider as a consumer, run a real
chat completion end-to-end.

**[VO]**
> "And this is the real test — opening a session and getting an actual inference
> response back, proving the entire chain works, not just each piece in isolation."

---

## Section 9: Confirming You Got Paid (13:00–14:00)

**[TEXT OVERLAY]** "MyProvider's dashboard doesn't show earnings yet — this is the
only reliable way to check, for now."

**[SCREEN]** Basescan, provider wallet address, showing an incoming payout
transaction after a session closes against this provider.

**[VO]**
> "Here's something worth knowing up front: the MyProvider GUI doesn't have a
> session-monitoring or earnings dashboard yet — that's a real, current gap, not
> something you're missing in the interface. Right now, checking your wallet's
> transaction history on Basescan is the reliable way to confirm you actually got
> paid for a session."

---

## Outro (14:00–14:30)

**[SCREEN]** Standard outro card.

**[VO]**
> "That's zero to earning. The full written guide has extensive troubleshooting for
> the parts most likely to go sideways — model server errors, firewall and NAT
> issues, and a couple of things worth double-checking that we didn't hit today.
> Linked below."

---

## YouTube description

Paste-ready. Replace `[GUIDE_URL]` with the production domain once one is decided —
**never** the `morpheus-asia.mintlify.site` review URL. Re-check chapter times
against the final edit (and split into two descriptions if you publish this as two
parts).

```text
Turn a spare machine into a Morpheus compute provider that earns MOR for serving AI
inference. We go from an empty Linux box to a registered, reachable provider, then
confirm a real payout on-chain. That last step matters: MyProvider doesn't have an
earnings dashboard yet, so on-chain is how you check.

Recorded against Lumerin Node v7.14.0 on native Linux with Ollama. Windows/WSL2
needs an extra port-forwarding step, which the written guide covers.

Chapters
0:00 What you'll build
0:25 Funding a wallet on Base
1:30 Getting the router
2:15 Native Linux vs. Windows/WSL2
3:00 Standing up a model server (Ollama)
5:00 Configuring the router and storing your key safely
7:00 Registering on-chain (the 0.3 MOR bid fee is non-refundable)
9:30 Opening the firewall and testing from outside
11:00 Health checks and a real end-to-end inference
13:00 Confirming you got paid
14:00 Next steps

Reload models-config.json without restarting (router v7.12+):
curl -s -u "$(cat ~/morpheus/.cookie)" -X POST http://localhost:8082/config/models/reload
(The router user making the call needs the system_config permission.)

Links
Written provider guide, with troubleshooting: [GUIDE_URL]/native-path/provider
Wallet setup: [GUIDE_URL]/native-path/wallet-setup
Getting MOR and ETH on Base: [GUIDE_URL]/native-path/funding-your-wallet
Router downloads (pick the latest release, not a "test" one): https://github.com/MorpheusAIs/Morpheus-Lumerin-Node/releases
Check which models are live on the network: https://active.mor.org/status
Questions? Morpheus Discord: https://discord.gg/kyVaxTHnvB

Never put a real private key in a file you screen-share or commit.
```
