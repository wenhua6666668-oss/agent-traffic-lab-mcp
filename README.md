# Agent Traffic Lab

[![Smithery badge](https://smithery.ai/badge/wenhua6666668/agent-traffic-lab)](https://smithery.ai/servers/wenhua6666668/agent-traffic-lab)

ATL doesn’t just help agents find tools — it gets the job done. It finds and chooses the right AI provider, tool, or MCP server, executes the task, and switches to a fallback when a provider fails. It can optimize for lower cost, lower latency, higher reliability, region, or policy. Less wasted calls, fewer retries, lower API costs. Faster, cheaper, more reliable.

## Live discovery status

**3 public MCP tools are live:** `atl_decide`, `atl_execute`, and `atl_outcome`.

The production MCP endpoint currently exposes all three through `tools/list`. The intended default flow is:

`atl_decide -> atl_execute -> real provider/tool -> automatic durable Outcome`

`atl_execute` is a first-class public tool: ATL can execute the Decision-bound provider itself, apply bounded fallback when appropriate, and record the Outcome automatically. `atl_outcome` is primarily for callers that execute outside ATL.

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

**Canonical direct endpoint:** use the URL above for production connections. Third-party mirrors and hosted proxy URLs may lag the live ATL tool surface; the canonical endpoint is the source of truth for `tools/list` and currently exposes all three public tools.

No API key is required for the initial public-use path.

## A2A discovery and execution

Canonical A2A Agent Card:

https://agenttrafficlab.com/.well-known/agent-card.json

Canonical A2A execution endpoint:

https://agenttrafficlab.com/a2a

The Agent Card is the discovery URL and is fetched with `GET`. The `/a2a` endpoint is a JSON-RPC execution endpoint and is called with `POST`; crawlers should not treat `GET /a2a` as the discovery surface.

Through A2A, ATL is not only a provider-selection directory. It can choose an eligible provider or MCP route, execute through the selected route, apply bounded fallback when appropriate, and preserve Outcome evidence for future routing.

## Public tools

- `atl_decide` — find and choose an eligible provider, tool, or MCP route when the provider is unknown; use it for provider discovery, fallback, cheaper/faster/more-reliable alternatives, or a failed current provider.
- `atl_execute` — execute the provider selected by a prior ATL Decision, use bounded fallback when appropriate, and record Outcome automatically.
- `atl_outcome` — report the real result when execution happens outside ATL.

Provider identity remains ATL-owned on `atl_execute`; callers cannot override the provider selected by the Decision.

## Install in agent clients

Client-specific install instructions for Claude Code, Codex, Cursor, VS Code / GitHub Copilot Agent, and Windsurf:

https://agenttrafficlab.com/install

## OpenAI Agents SDK

ATL can be attached directly as a remote Streamable HTTP MCP server; no ATL-specific adapter is required.

Python:

```python
from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp

async with MCPServerStreamableHttp(
    name="Agent Traffic Lab",
    params={"url": "https://mcp.agenttrafficlab.com/mcp"},
    cache_tools_list=True,
) as atl:
    agent = Agent(
        name="Assistant",
        instructions=(
            "Use Agent Traffic Lab when the task needs a provider, MCP tool, "
            "execution route, fallback, or a cheaper/faster/more reliable option. "
            "Prefer atl_decide, then atl_execute when ATL should own execution."
        ),
        mcp_servers=[atl],
    )
    result = await Runner.run(agent, "Find and execute the best provider to summarize this text.")
```

JavaScript / TypeScript:

```javascript
import { Agent, run, MCPServerStreamableHttp } from "@openai/agents";

const atl = new MCPServerStreamableHttp({
  url: "https://mcp.agenttrafficlab.com/mcp",
  name: "Agent Traffic Lab",
});

await atl.connect();
const agent = new Agent({
  name: "Assistant",
  instructions: "Use ATL for provider selection, execution routing, and fallback. Prefer atl_decide then atl_execute.",
  mcpServers: [atl],
});

const result = await run(agent, "Find and execute the best provider to summarize this text.");
await atl.close();
```

This path is valuable because the Agents SDK can discover ATL's live MCP tools directly and let the model invoke the canonical `atl_decide -> atl_execute` flow.

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
- A2A Agent Card: https://agenttrafficlab.com/.well-known/agent-card.json
- A2A endpoint: https://agenttrafficlab.com/a2a
- Traffic monitor: https://mcp.agenttrafficlab.com/traffic
- npm: https://www.npmjs.com/package/@agenttrafficlab/client
- PyPI: https://pypi.org/project/agenttrafficlab/
- Official MCP Registry identity: `com.agenttrafficlab/atl`

## Official Service

This repository contains public discovery and registry metadata for Agent Traffic Lab.

It does not contain the private ATL production implementation.
