# Agent Traffic Lab

Agent Traffic Lab (ATL) is public infrastructure for machine and AI-agent service discovery, decision, routing, execution, automatic Outcome recording, and outcome intelligence.

## Public MCP Endpoint

https://mcp.agenttrafficlab.com/mcp

## Install SDKs

JavaScript / npm:

```bash
npm install @agenttrafficlab/client
```

Python / PyPI:

```bash
pip install agenttrafficlab
```

## Core Capabilities

- `atl_decide` — discover and select a suitable machine-service provider for a task.
- `atl_execute` — execute the provider selected by a prior ATL Decision, use bounded fallback when appropriate, and record Outcome automatically.
- `atl_outcome` — report the real result when execution happens outside ATL.

Provider identity remains ATL-owned on `atl_execute`; callers cannot override the selected provider.

## MCP

Transport: Streamable HTTP  
Protocol: Model Context Protocol (MCP)

## Registry Identity

`com.agenttrafficlab/atl`

## Discovery Keywords

AI agents · MCP · Model Context Protocol · tool discovery · provider routing · agent routing · execution · automatic outcome · fallback · agent infrastructure · machine-service discovery

## Purpose

ATL provides a neutral machine-service routing layer designed to help agents discover, evaluate, route to, execute through, and learn from service providers.

The feedback loop is:

Discover → Inspect → Decide → Route → Execute → Outcome → Reputation → Decide again

For ATL-owned execution, `atl_execute` records the durable Outcome automatically. `atl_outcome` remains available when the caller executes externally.

## Links

- Homepage: https://agenttrafficlab.com
- Remote MCP: https://mcp.agenttrafficlab.com/mcp
- npm: https://www.npmjs.com/package/@agenttrafficlab/client
- PyPI: https://pypi.org/project/agenttrafficlab/
- Official MCP Registry identity: `com.agenttrafficlab/atl`

## Official Service

This repository contains public discovery and registry metadata for Agent Traffic Lab.

It does not contain the private ATL production implementation.
