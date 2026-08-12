# Agent Traffic Lab

Agent Traffic Lab (ATL) is public infrastructure for machine and AI-agent service discovery, decision, routing, execution feedback, and outcome intelligence.

## Public MCP Endpoint

https://mcp.agenttrafficlab.com/mcp

## Core Capabilities

- `atl_decide` — discover and select suitable machine-service providers for a task.
- `atl_outcome` — report execution outcomes back to ATL.

## MCP

Transport: Streamable HTTP  
Protocol: Model Context Protocol (MCP)

## Registry Identity

`com.agenttrafficlab/atl`

## Purpose

ATL provides a neutral machine-service decision layer designed to help agents discover, evaluate, route to, and learn from service providers.

The long-term feedback loop is:

Discover → Inspect → Decide → Route → Execute → Outcome → Reputation → Decide again

## Official Service

This repository contains public discovery and registry metadata for Agent Traffic Lab.

It does not contain the private ATL production implementation.
