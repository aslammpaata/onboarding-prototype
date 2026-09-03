# OpenClaw + EverClaw — Fresh Install Test Report

**Test date:** August 2026
**Tester environment:** WSL2 (Ubuntu) on Windows, isolated clean Linux user account
**OpenClaw version:** `2026.7.1-2` (CLI and config — matched)
**EverClaw version:** `2026.8.12.2040`
**Test objective:** validate the documented onboarding path from a genuine clean-user
perspective, and determine which previously-reported issues still reproduce.

> **Note on redaction:** all API keys, gateway auth tokens, device IDs, and wallet
> material in this report have been replaced with placeholders. No live credentials
> appear in this document.

---

## 1. Test Methodology

To test from a clean-user perspective without disturbing an existing working
installation, an **isolated Linux user account** was created within the existing WSL2
distribution:

```bash
# Stop the existing gateway to avoid port conflicts
openclaw gateway stop

# Create and switch to an isolated test user
sudo adduser openclawtest
sudo su - openclawtest
```

This provides a completely fresh `$HOME` — and therefore a fresh `~/.openclaw/`,
`~/.npm/`, and shell profile — while reusing system-level Node.js and git.

**Why this approach over the alternatives:**

| Approach | Assessment |
| --- | --- |
| Separate Linux user *(chosen)* | True config isolation, lightweight, reuses system deps |
| Second WSL distro | Heavier; `wsl --export/--import` clones the existing setup, defeating a clean test |
| Docker container | Adds port/networking complexity for a service that binds a gateway port |

**Pre-test verification:** confirmed `~/.openclaw/` did not exist and Node.js was
available in the new user's environment before beginning.

---

## 2. Environment Baseline

| Component | Detected |
| --- | --- |
| Platform | Linux (x86_64) |
| RAM | 6.6 GB total |
| Disk free | ~947 GB |
| GPU | None (CPU inference only) |
| curl | Present |
| git | 2.53.0 |
| Node.js | v24.15.0 |
| npm | 11.12.1 |
| OpenClaw | 2026.7.1-2 |

**Version consistency check:**

```bash
openclaw --version                                    → OpenClaw 2026.7.1-2
cat ~/.openclaw/openclaw.json | grep -i version       → lastRunVersion: 2026.7.1-2
                                                        lastTouchedVersion: 2026.7.1-2
```

CLI and config versions **matched**. This is relevant context: a previously reported set
of issues originated on a system where these two differed (config written by
`2026.7.1-2`, CLI running `2026.6.1`).

---

## 3. Installation Results

### 3.1 EverClaw Installation

Command: `curl -fsSL https://get.everclaw.xyz | bash`

**Completed successfully.** Installed components:

| Component | Result |
| --- | --- |
| EverClaw skill | ✅ Installed to `~/.openclaw/workspace/skills/everclaw` |
| Bootstrap starter key | ✅ Provisioned (`evcl_` format), stored `0600` at `~/.openclaw/.bootstrap-key` |
| Default model config | ✅ `mor-gateway/glm-5` configured automatically |
| Initial inference test | ✅ Passed during install |
| Morpheus proxy-router | ⚠️ **Install failed (non-fatal)** |
| Local Ollama fallback | ✅ Installed (`gemma4:e4b`) |
| Local embeddings | ✅ `node-llama-cpp@3.18.1` installed |
| MemPalace | ℹ️ Not installed (optional) |

**Bootstrap key characteristics:**

- Format: `evcl_...` (distinct from account keys, which use `sk-...`)
- Rate limit: 1,000 requests/day
- Expiry: fixed date ~30 days out
- Purpose: zero-config startup before the user supplies their own key

**Proxy-router failure detail:**

```text
Finding latest release...
Latest release: v7.9.0
Querying release assets...
  ⚠  Proxy-router install failed (not fatal)
    The API Gateway provides inference without it.
```

Confirmed non-blocking for API Gateway inference. Only affects the Native/P2P path.

### 3.2 Skill Directory Name — Resolved

```bash
$ ls ~/.openclaw/workspace/skills
everclaw
```

**Confirmed: `everclaw`**, not `everclaw-inference`. This settles a previously
ambiguous point. The `everclaw-inference` variant reported elsewhere did not appear
under this install method/version.

---

## 4. API Key Import

Command: `npm run bootstrap -- --key sk-[REDACTED]`

**Result: success.** Observed behaviour:

- Gateway connection tested and confirmed responding
- Bootstrap (`evcl_`) key file removed — "graduated" to the user's own key
- Config patched at `~/.openclaw/openclaw.json`
- Three models registered under the `mor-gateway` provider:
  - `mor-gateway/kimi-k2.5`
  - `mor-gateway/glm-4.7-flash`
  - `mor-gateway/llama-3.3-70b`
- `mor-gateway/kimi-k2.5` added to the fallback chain

**Config location for the API key** (confirmed):

```json
"models": {
  "providers": {
    "mor-gateway": {
      "baseUrl": "https://api.mor.org/api/v1",
      "apiKey": "[REDACTED]",
      "api": "openai-completions",
      "models": [ ... ]
    }
  }
}
```

---

## 5. 🆕 New Finding: Config Validation Failure on Fresh Install

**Severity: blocking.** On first `openclaw gateway start` after installation:

```text
Invalid config at ~/.openclaw/openclaw.json:
- gateway: Invalid input
OpenClaw config is invalid
Fix: openclaw doctor --fix
Gateway aborted: config is invalid.
```

**Root cause:** the generated config contains a `gateway.plugins.bonjour` block that
fails schema validation on this version:

```json
"plugins": {
  "bonjour": {
    "enabled": false
  }
}
```

**Resolution applied:** removed the block manually, then:

```bash
$ openclaw config validate
Config valid: ~/.openclaw/openclaw.json

$ openclaw gateway restart
Restarted systemd service: openclaw-gateway.service
```

**Assessment:** this is a **hard blocker on a fresh install**, not optional cleanup.
Existing documentation mentions removing this block, but does not convey that the
gateway will refuse to start until it is removed. `openclaw doctor --fix` is suggested
by the error output but was not the path validated here.

**Flag:** version-specific to `2026.7.1-2` until confirmed on other releases.

---

## 6. Model Configuration Testing

### 6.1 Can the primary model be changed without editing JSON? — **Yes**

Two supported methods were confirmed.

**Method A — CLI.** `openclaw models --help` exposes:

| Command | Purpose |
| --- | --- |
| `models set` | Set the default model |
| `models list` | List configured models |
| `models status` | Show configured model state |
| `models fallbacks` | Manage the fallback list |
| `models aliases` | Manage model aliases |
| `models auth` | Manage model auth profiles |

`openclaw config` additionally provides `get`, `set`, `patch`, `unset`, `validate`,
`schema`, and `file` for non-interactive config management.

**Method B — TUI slash command.** `/model mor-gateway/kimi-k2.5`

**Persistence test — passed.** After setting via `/model`:

1. Model change reflected immediately in the TUI status line
1. Session exited, `openclaw gateway restart` performed
1. On restart, the model **remained** `mor-gateway/kimi-k2.5`
1. A new session via `/new` also inherited the setting

**Conclusion:** `/model` persists across restarts and sessions. Hand-editing
`openclaw.json` is **not required** simply to change the primary model.

### 6.2 Model Availability

| Model | Pre-provisioned? | Inference test |
| --- | --- | --- |
| `mor-gateway/glm-5` | ✅ Yes (installer default) | ✅ Passed |
| `mor-gateway/kimi-k2.5` | ✅ Yes (bootstrap) | ✅ Passed |
| `mor-gateway/glm-4.7-flash` | ✅ Yes (bootstrap) | ✅ Passed |
| `mor-gateway/llama-3.3-70b` | ⚠️ Partial — declared under provider, **missing alias entry** | ✅ Passed after manual alias add |
| `mor-gateway/deepseek-v4-flash` | ❌ No — not present at all | ✅ Passed after full manual add |

**Two-part procedure required for models not fully provisioned:**

1. Declare the model under `models.providers.mor-gateway.models`:

```json
{
  "id": "deepseek-v4-flash",
  "name": "DeepSeek V4 Flash (via Morpheus Gateway)",
  "reasoning": false,
  "input": ["text"],
  "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
  "contextWindow": 131072,
  "maxTokens": 8192
}
```

1. Add an alias entry under `agents.defaults.models`:

```json
"mor-gateway/deepseek-v4-flash": {
  "alias": "DeepSeek V4 Flash (Gateway)",
  "streaming": true
}
```

Both models validated cleanly afterward (`openclaw config validate` → `Config valid`)
and returned successful inference.

### 6.3 Default Model — Previously-Reported Failure Not Reproduced

The previously documented `model too expensive for the hosted API gateway` error
affecting the default `glm-5` **did not reproduce**. Fresh install with a funded account
key completed bootstrap and returned successful inference on `mor-gateway/glm-5`.

**Conclusion:** the default model path is currently working. Documentation recommending
users immediately switch away from the default should be revised.

---

## 7. Diagnostic Output Analysis

`bash ~/.openclaw/workspace/skills/everclaw/scripts/diagnose.sh`
**Result: 10 passed, 1 warning, 5 failures (16 checks)**

**Failures that are EXPECTED in API-Gateway-only mode** (proxy-router was not installed):

| Reported failure | Assessment |
| --- | --- |
| Proxy-router not responding (port 8082) | ✅ Expected — P2P component only |
| Morpheus proxy not responding (port 8083) | ✅ Expected — same |
| Inference test timed out (`morpheus/kimi-k2.5`) | ✅ Expected — tests the P2P path, not the Gateway |
| Auth profiles file not found *(warning)* | ✅ Expected on fresh install |

**Critical check that passed:**

```text
✅ Morpheus API Gateway reachable (api.mor.org → HTTP 200)
```

**Genuine issues flagged, worth acting on:**

| Issue | Recommendation |
| --- | --- |
| `Morpheus models with reasoning: true (causes HTTP 400)` — affected `mor-gateway/glm-5` | Set `"reasoning": false` on all mor-gateway models |
| `11 model(s) missing streaming=true` — causes timeouts on slow connections | Run `node .../scripts/setup.mjs --apply`, or add `"streaming": true` manually |

**Documentation gap identified:** the diagnostic reports a failure count that looks
alarming on a healthy Gateway-only install. Users need guidance distinguishing expected
P2P-related failures from genuine problems.

---

## 8. EverClaw Skill Verification

```bash
$ openclaw skills info everclaw
everclaw ✓ Ready
  Source: openclaw-workspace
  Path: ~/.openclaw/workspace/skills/everclaw/SKILL.md
  Visible to model: yes
  Available as command: yes
```

**Previously reported metadata schema issue — NOT reproduced.** The reported symptom
(`environment: ✗ [object object]`, skill showing as not ready due to `requires.env`
declared as objects rather than a string array) did not occur. Inspection of `SKILL.md`
confirms `requires.env` **is** declared as a list of objects on this version, and the
skill loads correctly regardless.

**Assessment:** the earlier report almost certainly stemmed from the **CLI/config version
mismatch** on that system (config written by `2026.7.1-2`, read by CLI `2026.6.1`) rather
than a defect in the skill package itself.

**Model awareness test:** the agent correctly reported awareness of the skill, its path,
and its version, and correctly explained that it loads the full `SKILL.md` on demand
rather than eagerly — confirming the skill is visible in context as intended.

---

## 9. Summary of Findings

### Confirmed

1. `openclaw models set` and TUI `/model` both change the primary model **without editing JSON**; `/model` persists across restarts and sessions.
1. Default `mor-gateway/glm-5` **works** — the previously reported "too expensive" error did not reproduce.
1. Skill directory is **`everclaw`**.
1. **New blocking bug:** the `bonjour` plugin block fails config validation on fresh install; the gateway will not start until it is removed.
1. Bootstrap provisions only three models; `llama-3.3-70b` needs an alias entry, `deepseek-v4-flash` needs a full two-part manual add.
1. Proxy-router auto-install fails but is genuinely non-fatal for Gateway use.
1. Several `diagnose.sh` failures are expected in Gateway-only mode and require interpretation guidance.

### Not Reproduced (attributed to version mismatch on the original system)

- EverClaw metadata schema / `[object object]` failure
- `everclaw-inference` directory naming

### ⚠️ Still Unresolved — Requires Further Testing

**Fresh-account `insufficient credits` during bootstrap.** This test used a
**pre-existing account with credits already provisioned**, so it does **not** validate
the original failure mode (a brand-new account with no credits). A test with a genuinely
new account is still required before this can be marked resolved.

---

## 10. Recommended Next Tests

| Priority | Test | Rationale |
| --- | --- | --- |
| High | Bootstrap on a **genuinely new account** with no credits | Only remaining unvalidated onboarding blocker |
| Medium | Confirm the `bonjour` failure on other OpenClaw versions | Determines whether it's version-specific or general |
| Medium | Test `openclaw doctor --fix` as the `bonjour` remedy | May be a simpler documented fix than manual editing |
| Low | Retry proxy-router install to characterise the failure | Only affects the Native/P2P path |
| Low | Validate the same flow on macOS and native Linux | Current results are WSL2-only |
