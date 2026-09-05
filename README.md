# MorpheusGuide

MorpheusGuide is a local Mintlify prototype for the Morpheus onboarding experience. It provides journey-driven setup guides for people who want to use Morpheus inference, run the native P2P network, or become a compute provider.

This project is intended to sit above the existing Morpheus ecosystem and technical reference documentation. It focuses on practical onboarding, commands, screenshots, and troubleshooting rather than duplicating the full technical reference.

## Choose a Path

### API Gateway

Use the API Gateway path if you want to connect an application, AI agent, OpenClaw, or EverClaw to Morpheus-hosted models without running a local router or managing a wallet.

Platform guides are available for:

- Windows with WSL2 and Ubuntu
- macOS
- Ubuntu in an Oracle VirtualBox VM

These guides cover creating a Morpheus Inference API key, installing OpenClaw and EverClaw, configuring the gateway, and running a first chat.

### Native P2P

Use the Native Path if you want to run Morpheus locally and access the network directly. Before the setup guides, two prerequisite pages cover what every one of them assumes is already done:

- Wallet Setup & Seed Phrase Safety: creating/recovering a wallet and backing it up safely
- Getting MOR & ETH on Base: acquiring and bridging both assets onto the Base network

The setup guides themselves cover:

- Desktop App: beginner-friendly setup with a graphical interface
- `morctl`: command-line sessions and inference
- Headless / Developer: raw HTTP and `curl` integration
- Provider: registering compute, serving models, and earning MOR

The native path requires a funded wallet with MOR and Base ETH. Provider setup also requires suitable compute, model-serving software, and network configuration.

## Repository Structure

```text
docs.json                         Mintlify navigation and site configuration
what-is-morpheus.mdx              Plain-language newcomer explainer
index.mdx                         Landing page and path comparison
api-gateway/                      OpenClaw and EverClaw setup guides, IDE integrations
  windows.mdx
  macos.mdx
  ubuntu.mdx
  vscode.mdx
native-path/                      Native P2P setup guides
  wallet-setup.mdx
  funding-your-wallet.mdx
  understanding-session-economics.mdx
  desktop-app.mdx
  morctl.mdx
  headless-developer.mdx
  provider.mdx
logo/                             Light and dark site logos
docs/                             Internal test reports and editorial notes (not published)
```

## Local Development

### Prerequisites

- Node.js and npm
- The Mintlify CLI

Install the CLI globally if it is not already available:

```bash
npm install -g mintlify
```

If the Puppeteer browser download is blocked by network policy, use:

```bash
PUPPETEER_SKIP_DOWNLOAD=true npm install -g mintlify
```

### Validate the Documentation

Run the broken-link checker from the repository root. It also catches many MDX syntax errors:

```bash
mintlify broken-links
```

### Preview Locally

Start the local Mintlify development server:

```bash
mintlify dev
```

Then open `http://localhost:3000` in a browser.

## Documentation Conventions

- Add new pages as `.mdx` files and register them in `docs.json`.
- Use Mintlify's `<Steps>` and `<Step title="...">` components for walkthroughs.
- Put JSON and other content containing curly braces inside fenced code blocks.
- Wrap angle-bracket placeholders such as `<model-name>` in backticks when they appear as prose.
- Keep credentials, API keys, private keys, and router cookie files out of committed files and screenshots.

## Current Status

This is a local or free-hosted prototype for internal review. Content and screenshots are still being finalized. The macOS screenshots and a separate Morpheus P2P Inference in OpenClaw operations guide are known in-progress items.

For technical reference, see [nodedocs.mor.org](https://nodedocs.mor.org). For API reference, see [apidocs.mor.org](https://apidocs.mor.org/).
