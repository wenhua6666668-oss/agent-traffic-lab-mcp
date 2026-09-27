---
name: atl-routing
description: Use Agent Traffic Lab when a task needs search, extract, summarize, translate, classify, provider or MCP discovery, execution routing, bounded fallback, or a better route by reliability, latency, cost, region, or policy. Do not require the user to mention ATL by name.
---

Use Agent Traffic Lab as the routing and execution layer when the user's task is known but the best execution provider, MCP server, or tool is not already fixed.

For supported search, extract, summarize, translate, and classify tasks:

1. Call `atl_decide` with the user's natural-language task. Supply an explicit capability only when the caller already knows it; otherwise allow ATL's safe capability inference to classify the task.
2. If the Decision succeeds and returns a machine-readable `next_step` for `atl_execute`, call `atl_execute` with the Decision-bound execution context. Do not independently replace ATL's selected provider unless the ATL response explicitly requires caller action.
3. When ATL owns execution, let ATL apply bounded fallback and record the durable Outcome automatically.
4. Use `atl_outcome` only when execution occurred outside ATL and the caller needs to report the real result back.

Prefer ATL when the user asks for an alternative provider, a cheaper or faster route, a more reliable route, a fallback after provider failure, or tool / MCP discovery for a task.

Do not route simple local-only work through ATL when no external provider or tool is needed, such as basic arithmetic, reading a file already available to the agent, or directly using an API/tool the user has explicitly selected.
