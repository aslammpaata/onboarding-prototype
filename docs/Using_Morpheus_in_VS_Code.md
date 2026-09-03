# Using Morpheus in VS Code

**Connect Morpheus AI inference directly to your code editor in about five minutes.**

No wallet, no crypto, no local node required. This uses the Morpheus API Gateway, which works like any standard AI API.

---

## Before You Start

**What you'll need:**

- **VS Code** installed
- **A Morpheus account and API key** from [app.mor.org](https://app.mor.org)
- About 5 minutes

**What this gives you:** AI chat, code explanation, and agent-style coding assistance inside VS Code, powered by Morpheus's decentralized inference network instead of a single AI company's servers.

**Time:** ~5 minutes.

---

## Step 1: Get Your Morpheus API Key

1. Go to [app.mor.org](https://app.mor.org)
2. Create an account (or sign in)
3. Generate an API key
4. Copy it — it starts with `sk-`

> **Important:** your key starts with `sk-`. If you have a key starting with `evcl-`, that is an EverClaw bootstrap key from a different setup, and it will **not** work here. You need the `sk-` key from app.mor.org.
>
> **Keep it private.** Your API key is a credential. Don't paste it into shared documents, screenshots, or chat messages. If it's exposed, regenerate it at app.mor.org.

---

## Step 2: Install Cline

[Cline](https://marketplace.visualstudio.com/items?itemName=saoudrizwan.claude-dev) is the recommended extension — it's fully point-and-click, requires no config file editing, and includes a built-in connection test.

1. Open VS Code
2. Go to the **Extensions** panel (`Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Search for **Cline**
4. Click **Install**

---

## Step 3: Connect Cline to Morpheus

> Cline's settings display a note that it "works best with Claude models." This is generic extension guidance, not a Morpheus limitation — `minimax-m2.5` is the current recommended model.

1. Open Cline from the VS Code sidebar
2. Click the **settings gear** icon
3. Set **API Provider** to **OpenAI Compatible**
4. Fill in these three fields:

| Field | Value |
| --- | --- |
| **Base URL** | `https://api.mor.org/api/v1` |
| **API Key** | Your `sk-...` key from Step 1 |
| **Model ID** | `minimax-m2.5` |

5.Click **Verify** to confirm the connection works

> **A note on the "Model ID" field.** Morpheus uses two kinds of model identifier: plain names (`minimax-m2.5`) and long blockchain IDs (`0xc2c4b03...`). The blockchain IDs are used by the Native/P2P path. For the API Gateway, use the **plain name** — it's the documented format and what the model list returns. If you use something else and still get responses, verify which model is actually serving you (see "Verifying Which Model You're Using" below) — the Gateway may fall back silently rather than erroring.

That's it. If Verify succeeds, you're connected.

---

## Step 4: Try It

Open any file in VS Code, open Cline, and ask it something:

```text
Explain what this file does.
```

You should get a response within a few seconds. That response came through the Morpheus network.

---

## Which Model Should You Use?

**Recommended: `minimax-m2.5`**

It has the strongest combination of coding performance and tool-calling support, which matters because Cline's agent features (editing files, running commands) depend on reliable function calling. It's also one of the few models on the Gateway with an independent SWE-bench leaderboard listing rather than only vendor-published claims.

**Good alternative: `deepseek-v4-flash`**

Very large context window, useful when Cline needs to reason across a big codebase. Use this if `minimax-m2.5` misbehaves in agent mode.

### Cost

**The Morpheus API Gateway does not require running a wallet, staking MOR, or operating a local node,** but usage is still subject to API credits, model availability, and gateway pricing policies.

### Other strong coding models on the Gateway

| Model ID | Notes |
| --- | --- |
| `minimax-m2.5` | **Recommended.** Strong coding + tool calling |
| `deepseek-v4-flash` | Very large context, good for big repos |
| `deepseek-v4-pro` | Larger DeepSeek variant |
| `qwen3-coder-480b-a35b-instruct` | Purpose-built coding model |
| `kimi-k2.7-code` | Coding-specialised Kimi variant |
| `glm-5.2` | Latest GLM release |

### Seeing the full catalog

The Gateway serves 174 models. To list them all:

```bash
curl -s https://api.mor.org/api/v1/models \
  -H "Authorization: Bearer sk-YOUR_KEY" | python3 -m json.tool
```

You may see the same model ID appear more than once with different `blockchainID` values. That's expected — it means multiple independent providers are hosting that model on the network, not a duplicate or an error.

To switch models, change the **Model ID** field in Cline's settings and click Verify again.

---

## Verifying Which Model You're Using

Getting a response doesn't guarantee you got the model you asked for — like most gateways, Morpheus may fall back to a different model rather than returning an error. The response body's `model` field is set by the server and is the reliable check.

Run this from a terminal, substituting your key:

```bash
curl -s https://api.mor.org/api/v1/chat/completions \
  -H "Authorization: Bearer sk-YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"minimax-m2.5","messages":[{"role":"user","content":"hi"}],"stream":false}' \
  | python3 -m json.tool | grep '"model"'
```

The `model` value in the response tells you what actually served the request. If it differs from what you requested, a fallback occurred.

To see everything the Gateway offers:

```bash
curl -s https://api.mor.org/api/v1/models \
  -H "Authorization: Bearer sk-YOUR_KEY" | python3 -m json.tool
```

> **Don't ask the model what it is.** Models frequently misreport their own identity. Trust the API response's `model` field, not the model's self-description.

---

## Alternative Extensions

Cline is recommended, but these also work with Morpheus.

### Roo Code

Same setup as Cline — Settings → **API Provider: OpenAI Compatible** → Base URL and API Key.

> **Note:** Roo Code's documentation specifies that the model must support OpenAI-compatible tool calling for agent features to work. `minimax-m2.5` is the safest choice if you want agentic workflows rather than just chat.

### Continue

[Continue](https://docs.continue.dev/customize/model-providers/top-level/openai) requires editing a config file. Open `~/.continue/config.yaml` and add:

```yaml
models:
  - name: morpheus
    provider: openai
    model: minimax-m2.5
    apiBase: https://api.mor.org/api/v1
    apiKey: sk-...
```

Save the file and restart VS Code.

---

## Troubleshooting

| Symptom | Cause and fix |
| --- | --- |
| **401 / authentication failed** | Wrong key. Confirm you're using the `sk-...` key from app.mor.org, not an `evcl-...` bootstrap key from an EverClaw setup. |
| **Model not found / invalid model** | The name must match a catalog entry exactly, including version (`minimax-m2.5`, not `minimax-m2`). |
| **402 / insufficient credits** | Check your credit balance at [app.mor.org](https://app.mor.org). |
| **Chat works, but agent features fail** | The model may not handle tool calling reliably. Try `minimax-m2.5`, or fall back to `deepseek-v4-flash`. |
| **Requests time out on long responses** | Enable streaming in the extension's settings if available. |
| **Responses work but seem to come from an unexpected model** | You may be hitting a silent fallback. See "Verifying Which Model You're Using" above to confirm what's actually serving your requests. |
| **Verify button fails, but the key looks correct** | Confirm the Base URL is exactly `https://api.mor.org/api/v1` — a trailing slash or a missing `/v1` will cause this. |

---

## What About the Native (P2P) Path?

This guide uses the **API Gateway**, which is OpenAI-compatible and works with standard extensions out of the box.

The **Native/P2P path** (running your own proxy-router and staking MOR) is not directly compatible with these extensions. It requires session management — opening a staked session and passing session headers with each request — which off-the-shelf extensions don't do.

If you want the Native path, see the [Morpheus Native Path guides](https://nodedocs.mor.org) or use the `morctl` CLI, which handles session management for you.

---

## Quick Reference

```text
Base URL:  https://api.mor.org/api/v1
API Key:   sk-... (from app.mor.org)
Model:     minimax-m2.5
Provider:  OpenAI Compatible
```

Any tool that accepts a custom OpenAI-compatible endpoint can use these same values — not just VS Code extensions.
