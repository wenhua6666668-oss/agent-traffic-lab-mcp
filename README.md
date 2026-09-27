# Agent Traffic Lab

[![Smithery badge](https://smithery.ai/badge/wenhua6666668/agent-traffic-lab)](https://smithery.ai/servers/wenhua6666668/agent-traffic-lab)

ATL doesn’t just help agents find tools — it gets the job done. Give ATL a supported task such as **search, extract, summarize, translate, or classify**. ATL chooses an eligible provider, tool, or MCP server, executes the task, can use bounded fallback when appropriate, and records the execution Outcome when ATL owns execution.

## Fastest first use

Connect the canonical remote MCP endpoint:

`https://mcp.agenttrafficlab.com/mcp`

Then start with one natural-language argument:

`atl_decide({"task": "search for the latest critical CVE"})`

ATL can infer the safe V1 capability when possible. After a successful Decision, follow the returned machine-readable `next_step` to `atl_execute`.

Default loop:

`ordinary task -> atl_decide(task) -> next_step -> atl_execute -> real provider/tool -> automatic durable Outcome`

No API key is required for the initial public-use path.

## Live discovery status

**3 public MCP tools are live:** `atl_decide`, `atl_execute`, and `atl_outcome`.

The production MCP endpoint exposes all three through `tools/list`.

`atl_execute` is a first-class public tool: ATL can execute the Decision-bound provider itself, apply bounded fallback when appropriate, and record the Outcome automatically. `atl_outcome` is primarily for callers that execute outside ATL.

## When to use ATL

- Search for current information through an eligible provider.
- Extract structured data or fields through an eligible provider.
- Summarize content through an eligible provider.
- Translate text through an eligible provider.
- Classify text or a support ticket through an eligible provider.
- Find a provider, MCP server, or tool for a task.
- The current provider failed; find and use a bounded fallback.
- Use a cheaper, faster, or more reliable eligible provider.
- Choose between multiple AI providers or MCP routes.
- Prefer a route based on reliability, latency, cost, region, or policy.
- Let ATL choose and execute the route instead of selecting a provider manually.

ATL is a routing and execution layer, not a standalone search engine or generic calculator. Its role is to choose an eligible execution provider for the task and, when requested, carry the execution through.

## Public MCP Endpoint

https://mcp.agenttrafficlab.com/mcp

**Canonical direct endpoint:** use the URL above for production connections. Third-party mirrors and hosted proxy URLs may lag the live ATL tool surface; the canonical endpoint is the source of truth for `tools/list` and currently exposes all three public tools.

## A2A discovery and execution

Canonical A2A Agent Card:

https://agenttrafficlab.com/.well-known/agent-card.json

Canonical A2A execution endpoint:

https://agenttrafficlab.com/a2a

The Agent Card is the discovery URL and is fetched with `GET`. The `/a2a` endpoint is a JSON-RPC execution endpoint and is called with `POST`; crawlers should not treat `GET /a2a` as the discovery surface.

Through A2A, ATL is not only a provider-selection directory. It can choose an eligible provider or MCP route, execute through the selected route, apply bounded fallback when appropriate, and preserve Outcome evidence for future routing.

## Tools

- `atl_decide` — route a supported natural-language task to an eligible provider, tool, or MCP route. For public first use, only `task` is required; ATL can infer `search`, `extract`, `summarize`, `translate`, or `classify` when possible. A successful response includes a machine-readable `next_step` for `atl_execute`.
- `atl_execute` — execute the provider selected by a prior ATL Decision, use bounded fallback when appropriate, and record Outcome automatically.
- `atl_outcome` — report the real result when execution happens outside ATL.

Provider identity remains ATL-owned on `atl_execute`; callers cannot override the provider selected by the Decision.

## Install in agent clients

Client-specific install instructions are available for Claude Code, Codex, Cursor, VS Code / GitHub Copilot Agent, Windsurf, OpenAI Agents SDK, Google ADK, Microsoft Agent Framework, PydanticAI, LangChain / LangGraph, and Anthropic Claude API MCP Connector:

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
            "Use Agent Traffic Lab for supported search, extract, summarize, translate, "
            "or classify tasks when ATL should choose an execution provider. "
            "Start with atl_decide and follow its next_step to atl_execute."
        ),
        mcp_servers=[atl],
    )
    result = await Runner.run(agent, "Search for the latest critical CVE.")
```

## Google Agent Development Kit (ADK)

Google ADK can also connect ATL directly over Streamable HTTP MCP; no ATL-specific adapter is required.

```python
from google.adk.agents import Agent
from google.adk.tools.mcp_tool import McpToolset, StreamableHTTPConnectionParams

atl = McpToolset(
    connection_params=StreamableHTTPConnectionParams(
        url="https://mcp.agenttrafficlab.com/mcp"
    )
)

root_agent = Agent(
    name="atl_routed_agent",
    model="gemini-2.5-flash",
    instruction=(
        "Use Agent Traffic Lab for supported tasks when ATL should choose and execute "
        "the provider. Start with atl_decide and follow next_step to atl_execute."
    ),
    tools=[atl],
)
```

## Microsoft Agent Framework

Microsoft Agent Framework can connect directly to ATL with `MCPStreamableHTTPTool`; no ATL-specific adapter is required.

```python
from agent_framework import Agent, MCPStreamableHTTPTool
from agent_framework.openai import OpenAIChatClient

async with (
    MCPStreamableHTTPTool(
        name="Agent Traffic Lab",
        url="https://mcp.agenttrafficlab.com/mcp",
    ) as atl,
    Agent(
        client=OpenAIChatClient(),
        name="ATL-routed agent",
        instructions=(
            "Use Agent Traffic Lab for supported tasks when ATL should choose and execute "
            "the provider. Start with atl_decide and follow next_step to atl_execute."
        ),
    ) as agent,
):
    result = await agent.run(
        "Extract the structured fields from this page.",
        tools=atl,
    )
```

Microsoft Agent Framework also supports A2A remote agents, so ATL's public Agent Card can be used when an application prefers an A2A integration instead of MCP.

## PydanticAI

PydanticAI can connect to ATL directly as a remote Streamable HTTP MCP toolset; no ATL-specific adapter is required.

```python
from pydantic_ai import Agent
from pydantic_ai.mcp import MCPToolset

atl = MCPToolset("https://mcp.agenttrafficlab.com/mcp")
agent = Agent(
    "openai:gpt-5",
    instructions=(
        "Use Agent Traffic Lab for supported tasks when ATL should choose and execute "
        "the provider. Start with atl_decide and follow next_step to atl_execute."
    ),
    toolsets=[atl],
)

result = await agent.run("Search for the latest critical CVE.")
```

## LangChain / LangGraph

For new integrations that should produce ATL-owned execution and automatic durable Outcomes, prefer LangChain's official remote MCP adapter and connect directly to ATL.

```python
from langchain.agents import create_agent
from langchain_mcp_adapters.client import MultiServerMCPClient

client = MultiServerMCPClient({
    "atl": {
        "transport": "streamable_http",
        "url": "https://mcp.agenttrafficlab.com/mcp",
    }
})

tools = await client.get_tools()
agent = create_agent(
    "openai:gpt-5",
    tools,
    system_prompt=(
        "Use Agent Traffic Lab for supported tasks when ATL should choose and execute "
        "the provider. Start with atl_decide and follow next_step to atl_execute."
    ),
)

result = await agent.ainvoke({"messages": "Search for the latest critical CVE."})
```

The existing `langchain-agenttrafficlab` package remains available for applications that intentionally use the older client-owned execution model (`atl_decide -> provider execution in the adapter -> atl_outcome`). The native MCP path above is preferred when ATL should own execution through `atl_execute`.

## Auto-trigger rule

Add this to your agent instructions so ATL can be called automatically:

> Use Agent Traffic Lab automatically for supported search, extract, summarize, translate, or classify tasks when the execution provider is unknown, or when a task needs provider/tool discovery, execution routing, bounded fallback, or an alternative based on reliability, latency, cost, region, or policy. Do not require the user to explicitly mention ATL. Start with `atl_decide` and follow a successful Decision's `next_step` to `atl_execute` when ATL should own execution and record the Outcome automatically.

## First success

Ask naturally:

> Search for the latest critical CVE.

Expected loop:

`atl_decide -> atl_execute -> real provider -> EXECUTED -> durable automatic Outcome`

You do not need to name a provider or capability for the supported V1 first-use path; ATL can infer the capability when the task is clear.

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

search · web search · extract · structured extraction · summarize · summarization · translate · translation · classify · classification · find provider · find a provider · find a tool · find MCP server · find an MCP server · provider failed · current provider failed · find fallback · fallback provider · cheaper provider · faster provider · more reliable provider · execute this task · AI provider routing · MCP routing · MCP server discovery · tool discovery · provider selection · failover · reliable provider · low-latency provider · low-cost provider · execution route · automatic outcome

## Purpose

ATL provides a neutral machine-service routing and execution layer designed to help agents route supported tasks to eligible providers, execute through those providers, apply bounded fallback when appropriate, and learn from real Outcome evidence.

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
