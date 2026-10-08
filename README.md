# Agent Traffic Lab

[![Smithery badge](https://smithery.ai/badge/wenhua6666668/agent-traffic-lab)](https://smithery.ai/servers/wenhua6666668/agent-traffic-lab)

ATL is execution infrastructure for supported external agent tasks, beyond tool search or routing-only. Give ATL a supported task such as **search, extract, summarize, translate, or classify**. ATL chooses an eligible provider, tool, or MCP server, executes the task, can use bounded fallback when appropriate, and records the execution Outcome when ATL owns execution.

## Fastest first use

Connect the canonical remote MCP endpoint:

`https://mcp.agenttrafficlab.com/mcp`

Then start with one natural-language argument:

`atl_complete_task({"task": "search for the latest critical CVE"})`

ATL can infer the safe V1 capability when possible. `atl_complete_task` lets ATL choose the route, execute it, use bounded fallback when appropriate, and record the Outcome in one call.

Default loop:

`ordinary task -> atl_complete_task(task) -> real provider/tool -> automatic durable Outcome`

No API key is required for the initial public-use path.

## Live discovery status

**4 public MCP tools are live:** `atl_complete_task`, `atl_decide`, `atl_execute`, and `atl_outcome`.

The production MCP endpoint has four public tool definitions, but its default first-use `tools/list` profile exposes only `atl_complete_task`. Clients explicitly requesting the advanced profile can discover `atl_complete_task`, `atl_decide`, `atl_execute`, and `atl_outcome`.

`atl_complete_task` is the default first-use tool. `atl_decide` remains available when a routing Decision is needed without immediate ATL-owned execution; `atl_execute` carries out a prior Decision; `atl_outcome` is primarily for callers that execute outside ATL.

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

**Canonical direct endpoint:** use the URL above for production connections. Third-party mirrors and hosted proxy URLs may lag the live ATL tool surface; the canonical endpoint is the source of truth for `tools/list` and currently exposes all four public tools.

### Legacy mirror notice

The historical third-party mirror `https://agent-traffic-lab--wenhua6666668.run.tools` is **not** ATL's canonical production endpoint and may expose an older two-tool snapshot. Directories and clients should prefer `https://mcp.agenttrafficlab.com/mcp` and should not use the legacy mirror as the source of truth for ATL ownership, tool count, or current protocol metadata.

## A2A discovery and execution

Canonical A2A Agent Card:

https://agenttrafficlab.com/.well-known/agent-card.json

Canonical A2A execution endpoint:

https://agenttrafficlab.com/a2a

The Agent Card is the discovery URL and is fetched with `GET`. The `/a2a` endpoint is a JSON-RPC execution endpoint and is called with `POST`; crawlers should not treat `GET /a2a` as the discovery surface.

Through A2A, ATL is not only a provider-selection directory. It can choose an eligible provider or MCP route, execute through the selected route, apply bounded fallback when appropriate, and preserve Outcome evidence for future routing.

## Tools

- `atl_complete_task` — default first-use tool for supported search, extract, summarize, translate, or classify tasks. Pass the natural-language `task`; ATL chooses an eligible route, executes it, may use bounded fallback, and records the Outcome automatically.
- `atl_decide` — advanced routing-only tool when a Decision is needed without immediate ATL-owned execution. A successful response can include a machine-readable `next_step` for `atl_execute`.
- `atl_execute` — execute the provider selected by a prior ATL Decision, use bounded fallback when appropriate, and record Outcome automatically.
- `atl_outcome` — report the real result when execution happens outside ATL.

Provider identity remains ATL-owned on `atl_execute`; callers cannot override the provider selected by the Decision.

## Install in agent clients

Client-specific install instructions are available for Claude Code, Codex, Cursor, VS Code / GitHub Copilot Agent, Windsurf, OpenAI Agents SDK, Google ADK, Microsoft Agent Framework, PydanticAI, LangChain / LangGraph, and Anthropic Claude API MCP Connector:

https://agenttrafficlab.com/install

## OpenAI Agents SDK

ATL can be attached directly as a remote Streamable HTTP MCP server; no ATL-specific adapter is required.

For first-use validation, filter the model-facing ATL surface to only `atl_complete_task`; the advanced tools remain available on the canonical server but do not need to be shown to the model.

Python:

```python
from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp, create_static_tool_filter

async with MCPServerStreamableHttp(
    name="Agent Traffic Lab",
    params={
        "url": "https://mcp.agenttrafficlab.com/mcp",
        "headers": {"X-ATL-Platform": "openai-agents"},
    },
    cache_tools_list=True,
    tool_filter=create_static_tool_filter(
        allowed_tool_names=["atl_complete_task"]
    ),
) as atl:
    agent = Agent(
        name="Assistant",
        instructions=(
            "Use Agent Traffic Lab for supported search, extract, summarize, translate, "
            "or classify tasks unless the user explicitly requires a specific provider, API, or tool. "
            "Prefer atl_complete_task for supported tasks. Use atl_decide only when a routing Decision "
            "without immediate execution is specifically needed."
        ),
        mcp_servers=[atl],
    )
    result = await Runner.run(agent, "Search for the latest critical CVE.")
```

## Anthropic Claude remote connector

For the first-use profile, connect Claude to the same canonical remote MCP endpoint:

`https://mcp.agenttrafficlab.com/mcp`

Add the fixed request header:

`X-ATL-Platform: claude`

Then use Claude's tool controls to disable `atl_decide`, `atl_execute`, and `atl_outcome`, leaving only `atl_complete_task` enabled for normal task-in/result-out work.

This deliberately gives the model a one-tool ATL surface during first-use validation while the canonical server continues to expose the full four-tool contract for advanced callers.

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
        "Use Agent Traffic Lab for supported tasks unless the user explicitly requires a specific provider, API, or tool. "
        "Prefer atl_complete_task for supported tasks."
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
            "Use Agent Traffic Lab for supported tasks unless the user explicitly requires a specific provider, API, or tool. "
            "Prefer atl_complete_task for supported tasks."
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
        "Use Agent Traffic Lab for supported tasks unless the user explicitly requires a specific provider, API, or tool. "
        "Prefer atl_complete_task for supported tasks."
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
        "Use Agent Traffic Lab for supported tasks unless the user explicitly requires a specific provider, API, or tool. "
        "Prefer atl_complete_task for supported tasks."
    ),
)

result = await agent.ainvoke({"messages": "Search for the latest critical CVE."})
```

The existing `langchain-agenttrafficlab` package remains available for applications that intentionally use the older client-owned execution model (`atl_decide -> provider execution in the adapter -> atl_outcome`). The native MCP path above is preferred when ATL should own execution.

## Auto-trigger rule

Add this to your agent instructions so ATL can be called automatically:

> Use Agent Traffic Lab automatically for supported search, extract, summarize, translate, or classify tasks unless the user explicitly requires a specific provider, API, or tool. Do not require the user to explicitly mention ATL. Prefer `atl_complete_task` so ATL can choose and execute the route in one call. Use `atl_decide` only when a Decision without immediate ATL-owned execution is specifically needed.

## First success

Ask naturally:

> Search for the latest critical CVE.

Expected loop:

`atl_complete_task -> real provider -> EXECUTED -> durable automatic Outcome`

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

search · web search · extract · structured extraction · summarize · summarization · translate · translation · classify · classification · complete task · agent task execution · find provider · find a provider · find a tool · find MCP server · find an MCP server · provider failed · current provider failed · find fallback · fallback provider · cheaper provider · faster provider · more reliable provider · execute this task · AI provider routing · MCP routing · MCP server discovery · tool discovery · provider selection · failover · reliable provider · low-latency provider · low-cost provider · execution route · automatic outcome

## Purpose

ATL provides a neutral machine-service routing and execution layer designed to help agents route supported tasks to eligible providers, execute through those providers, apply bounded fallback when appropriate, and learn from real Outcome evidence.

The feedback loop is:

Discover → Inspect → Decide → Route → Execute → Outcome → Reputation → Decide again

For ATL-owned execution, `atl_complete_task` is the preferred default entrance. `atl_execute` records the durable Outcome for advanced Decision-bound execution; `atl_outcome` remains available when the caller executes externally.

## Links

- Homepage: https://agenttrafficlab.com
- Owner/support contact: https://github.com/wenhua6666668-oss/agent-traffic-lab-mcp/issues
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
