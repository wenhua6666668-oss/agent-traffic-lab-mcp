# Agent Traffic Lab

Agent Traffic Lab (ATL) is public infrastructure for machine and AI-agent service discovery, decision, routing, execution feedback, and outcome intelligence.

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

- `atl_decide` — discover and select suitable machine-service providers for a task.
- `atl_outcome` — report execution outcomes back to ATL.

## MCP

Transport: Streamable HTTP  
Protocol: Model Context Protocol (MCP)

## Registry Identity

`com.agenttrafficlab/atl`

## Discovery Keywords

AI agents · MCP · Model Context Protocol · tool discovery · provider routing · agent routing · fallback · agent infrastructure · machine-service discovery

## Purpose

ATL provides a neutral machine-service decision layer designed to help agents discover, evaluate, route to, and learn from service providers.

The long-term feedback loop is:

Discover → Inspect → Decide → Route → Execute → Outcome → Reputation → Decide again

## Links

- Homepage: https://agenttrafficlab.com
- Remote MCP: https://mcp.agenttrafficlab.com/mcp
- npm: https://www.npmjs.com/package/@agenttrafficlab/client
- PyPI: https://pypi.org/project/agenttrafficlab/
- Official MCP Registry identity: `com.agenttrafficlab/atl`

## Official Service

This repository contains public discovery and registry metadata for Agent Traffic Lab.

It does not contain the private ATL production implementation.
