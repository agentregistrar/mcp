# Agent Registrar Identity Broker MCP

[![Agent Registrar MCP connector](https://glama.ai/mcp/connectors/dev.agentregistrar/agent-registrar/badges/score.svg)](https://glama.ai/mcp/connectors/dev.agentregistrar/agent-registrar)
[![mcptry](https://mcptry.com/badge/dev.agentregistrar/agentregistrar)](https://mcptry.com/servers/dev.agentregistrar/agentregistrar)

Let your AI agents sign up for software and cloud services without getting stuck on human signup forms. Agent Registrar is an agent identity broker for the open [Agent Identity Protocol](https://github.com/agentregistrar/spec). It gives each AI agent its own identity, dedicated email inbox, emailed copies of accepted terms, audit trail, and kill switch under your verified ownership, so providers can allow autonomous agent signups while keeping a real human accountable and in control. Providers keep their own signup API, account, credentials, pricing, and product experience.

This repository holds the public documentation and registry manifests (`server.json`, `glama.json`, `smithery.yaml`) for the hosted remote MCP server at `https://agentregistrar.dev/mcp`. The server exposes five tools and four static resources over Streamable HTTP with OAuth 2.1. There is no local process, no package install, and no static API key.

- Remote MCP endpoint: `https://agentregistrar.dev/mcp`
- Discovery manifest: `https://agentregistrar.dev/.well-known/mcp.json`
- Client install guide: `https://docs.agentregistrar.dev/agents/install/`
- Quickstart: `https://docs.agentregistrar.dev/agents/quickstart/`
- Public specification: `https://github.com/agentregistrar/spec`
- Live reference provider (VaporCloud): `https://skdl.dev` (`https://skdl.dev/.well-known/agent-signup.json`)

## Tools

| Tool | What it does |
| :--- | :--- |
| `identity_whoami` | Shows the active agent, its inbox address and scopes, the verified human owner, recent signups, services signed up under other agents of the same human (`human_other_agent_signups`), and the latest acceptance per service. Read-only. |
| `identity_signup` | Fetches the provider's discovery document and terms, records the delegated acceptance, and returns a prepared POST request plus a short-lived signed `purpose: "signup"` identity assertion. Send the request exactly as returned. |
| `identity_login` | Day-2 re-authentication for services publishing `endpoints.agent_login` (or attaching a new agent under the same human). Checks re-login policy and terms freshness, re-attests drifted terms when allowed, and returns a prepared POST request with a fresh 120s `purpose: "login"` assertion. |
| `identity_record_signup` | Records a provider-signed `signup_token` receipt. Recording is idempotent, and the broker never stores provider credentials. |
| `inbox_read` | Lists, reads, or waits for inbound agent email, including OTP detection. |

## Static resources

| Resource | URI | Contents |
| :--- | :--- | :--- |
| `quickstart` | `agentregistrar://docs/quickstart` | The end-to-end Day-1 and Day-2 workflow: check identity, sign up or re-login, send the prepared request, handle `handoff` and step-up responses, record the receipt, and read the inbox. |
| `assertion-claims` | `agentregistrar://docs/assertion-claims` | The signed assertion claim set (including `purpose` and `accepted_rp_tos_version`), a worked example, and relying-party verification steps. |
| `error-recovery` | `agentregistrar://docs/error-recovery` | The tool error envelope and the stable error codes (32 codes) with retryability and next steps. |
| `spec` | `agentregistrar://docs/spec` | A short `v2.2.0` protocol summary and canonical specification, guide, and docs links. |

## Install

Every client below connects to `https://agentregistrar.dev/mcp` using standard OAuth 2.1 with PKCE `S256`. The broker supports Client ID Metadata Documents (CIMD) and Dynamic Client Registration (DCR), so there is no client ID or API key to copy.

### Claude Code

```bash
claude mcp add --transport http agentregistrar https://agentregistrar.dev/mcp
```

Then run `/mcp` inside Claude Code to connect through the browser.

### Cursor

Add to `.cursor/mcp.json` or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "agentregistrar": {
      "url": "https://agentregistrar.dev/mcp"
    }
  }
}
```

Reload the window, start `agentregistrar` from MCP settings, and authenticate in the browser.

### OpenCode

```bash
opencode mcp add agentregistrar --global --url https://agentregistrar.dev/mcp
```

Then run `/mcps` inside OpenCode to connect through OAuth.

### Codex CLI

```bash
codex mcp add agentregistrar --url https://agentregistrar.dev/mcp
codex mcp login agentregistrar
```

### Gemini CLI

```bash
gemini mcp add --transport http agentregistrar https://agentregistrar.dev/mcp
```

Then run `/mcp auth agentregistrar` inside Gemini CLI.

### Antigravity CLI

Add to `~/.gemini/config/mcp_config.json` or `.agents/mcp_config.json`:

```json
{
  "mcpServers": {
    "agentregistrar": {
      "serverUrl": "https://agentregistrar.dev/mcp"
    }
  }
}
```

Open `/mcp` and choose Authenticate next to `agentregistrar`.

### VS Code / Copilot

```bash
code --add-mcp '{"name":"agentregistrar","type":"http","url":"https://agentregistrar.dev/mcp"}'
```

Start the server from `MCP: List Servers` and sign in through the browser prompt.

### Zed

Add to `settings.json` under `context_servers`:

```json
{
  "context_servers": {
    "agentregistrar": {
      "url": "https://agentregistrar.dev/mcp"
    }
  }
}
```

Zed opens browser OAuth on first tool use.

### OpenClaw

```bash
openclaw mcp set agentregistrar '{"url":"https://agentregistrar.dev/mcp","transport":"streamable-http","auth":"oauth"}'
openclaw mcp login agentregistrar
```

### Hermes

Add to `~/.hermes/config.yaml`:

```yaml
mcp_servers:
  agentregistrar:
    url: "https://agentregistrar.dev/mcp"
    auth: oauth
    enabled: true
```

Then run `hermes mcp login agentregistrar`.

### Cline

Add to `cline_mcp_settings.json`:

```json
{
  "mcpServers": {
    "agentregistrar": {
      "type": "streamableHttp",
      "url": "https://agentregistrar.dev/mcp",
      "disabled": false
    }
  }
}
```

Click Authenticate in Cline to complete browser OAuth.

### Goose

Add to `~/.config/goose/config.yaml`:

```yaml
mcp:
  extensions:
    agentregistrar:
      name: "Agent Registrar"
      type: streamable_http
      uri: "https://agentregistrar.dev/mcp"
      enabled: true
```

Goose follows the `401` OAuth discovery challenge and opens the browser.

### Claude Desktop and Claude.ai

Open Customize > Connectors > Add custom connector:

```text
Name: Agent Registrar
Remote MCP server URL: https://agentregistrar.dev/mcp
OAuth client: Use Claude's published identity (CIMD), or Register automatically (DCR)
```

### ChatGPT

Enable Developer mode in ChatGPT settings, open Apps / Connectors > Add custom MCP server:

```text
Name: Agent Registrar
Connection URL: https://agentregistrar.dev/mcp
Authentication: OAuth
```

## Scope boundaries

Agent Registrar is identity-only. It is not a marketplace, a credential vault, a wallet, a provisioning API, or a bot-detection service. The broker does not sit in a provider's request path, does not store provider credentials, and does not delete or disable accounts inside a provider. A per-agent kill switch stops new assertions and inbox access; it does not remove an existing provider account.

`human.verified` means a verified email address, not legal identity proofing. Provider verification is optional; a verified provider is not thereby safe, approved, or endorsed. A directory listing is not an endorsement.

Read the full boundaries at `https://docs.agentregistrar.dev/start/trust-and-limits/`.

## License

The documentation and manifests in this repository, along with the Agent Identity Protocol specification, conformance validator, and public docs, are licensed under Apache-2.0. The hosted broker at `https://agentregistrar.dev` is a service; the broker codebase is private.
