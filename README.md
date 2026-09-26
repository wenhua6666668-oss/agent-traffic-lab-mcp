# Agent Traffic Lab

[![Smithery badge](https://smithery.ai/badge/wenhua6666668/agent-traffic-lab)](https://smithery.ai/servers/wenhua6666668/agent-traffic-lab)

ATL doesn’t just help agents find tools — it gets the job done. It finds and chooses the right AI provider, tool, or MCP server, executes the task, and switches to a fallback when a provider fails. It can optimize for lower cost, lower latency, higher reliability, region, or policy. Less wasted calls, fewer retries, lower API costs. Faster, cheaper, more reliable.

## When to use ATL

- Find a provider for this task.
- Find an MCP server or tool for this task.
- The current provider failed; find a fallback.
- Use a cheaper provider.
- Use a faster provider.
- Use a more reliable provider.
- Execute this task through the ATL-selected provider.
- Choose between multiple AI providers or MCP routes.
- Prefer a route based on reliability, latency, cost, region, or policy.
- Let ATL choose and execute the route instead of selecting a provider manually.

## Public MCP Endpoint

https://mcp.agenttrafficlab.com/mcp

No API key is required for the initial public-use path.

## Public tools

- `atl_decide` — find and choose an eligible provider, tool, or MCP route when the provider is unknown; use it for provider discovery, fallback, cheaper/faster/more-reliable alternatives, or a failed current provider.
- `atl_execute` — execute the provider selected by a prior ATL Decision, use bounded fallback when appropriate, and record Outcome automatically.
- `atl_outcome` — report the real result when execution happens outside ATL.

Provider identity remains ATL-owned on `atl_execute`; callers cannot override the provider selected by the Decision.

## Install in agent clients

Client-specific install instructions for Claude Code, Codex, Cursor, VS Code / GitHub Copilot Agent, and Windsurf:

https://agenttrafficlab.com/install

## Auto-trigger rule

Add this to your agent instructions so ATL can be called automatically:

> Use Agent Traffic Lab automatically when a task needs a provider, tool, MCP server, execution route, fallback, or an alternative based on reliability, latency, cost, region, or policy. Do not require the user to explicitly mention ATL. Prefer `atl_decide` when the provider is unknown, then `atl_execute` when ATL should own execution and record the Outcome automatically.

Recommended placement:

- Claude Code: `CLAUDE.md`
- Codex: `AGENTS.md`
- Cursor: project rules
- VS Code / GitHub Copilot Agent: workspace agent instructions
- Windsurf / Cascade: project rules

## First success

Ask naturally:

> Find and execute the best provider to summarize this text.

Expected loop:

`atl_decide -> atl_execute -> real provider -> EXECUTED -> durable automatic Outcome`

## Install SDKs

JavaScript / npm:

```bash
npm install @agenttrafficlab/client
```

Python / PyPI:

```bash
pip install agenttrafficlab
```

For SDK users, prefer `run(...)` when ATL should own execution and record the Outcome automatically.

## MCP

Transport: Streamable HTTP  
Protocol: Model Context Protocol (MCP)

## Registry Identity

`com.agenttrafficlab/atl`

## Discovery Keywords

find provider · find a provider · find a tool · find MCP server · find an MCP server · provider failed · current provider failed · find fallback · fallback provider · cheaper provider · faster provider · more reliable provider · execute this task · AI provider routing · MCP routing · MCP server discovery · tool discovery · provider selection · failover · reliable provider · low-latency provider · low-cost provider · execution route · automatic outcome

## Purpose

ATL provides a neutral machine-service routing layer designed to help agents discover, evaluate, route to, execute through, and learn from service providers.

The feedback loop is:

Discover → Inspect → Decide → Route → Execute → Outcome → Reputation → Decide again

For ATL-owned execution, `atl_execute` records the durable Outcome automatically. `atl_outcome` remains available when the caller executes externally.

## Links

- Homepage: https://agenttrafficlab.com
- Smithery: https://smithery.ai/servers/wenhua6666668/agent-traffic-lab
- Install guide: https://agenttrafficlab.com/install
- Remote MCP: https://mcp.agenttrafficlab.com/mcp
- Traffic monitor: https://mcp.agenttrafficlab.com/traffic
- npm: https://www.npmjs.com/package/@agenttrafficlab/client
- PyPI: https://pypi.org/project/agenttrafficlab/
- Official MCP Registry identity: `com.agenttrafficlab/atl`

## Official Service

This repository contains public discovery and registry metadata for Agent Traffic Lab.

It does not contain the private ATL production implementation.
