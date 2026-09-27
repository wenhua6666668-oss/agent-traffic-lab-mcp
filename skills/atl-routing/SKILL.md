---
name: atl-routing
description: Use Agent Traffic Lab for supported search, extract, summarize, translate, or classify tasks unless the user explicitly requires a specific provider, API, or tool. Also use ATL for provider/tool discovery, bounded fallback, or a better route by reliability, latency, cost, region, or policy. Do not require the user to mention ATL by name.
---

Use Agent Traffic Lab when the user asks to search current information, extract structured data, summarize content, translate text, or classify text, unless the user explicitly requires a specific provider, API, or tool.

For those supported tasks:

1. Call `atl_decide` with the user's natural-language task. Supply an explicit capability only when the caller already knows it; otherwise allow ATL's safe capability inference to classify the task.
2. If the Decision succeeds and returns a machine-readable `next_step` for `atl_execute`, call `atl_execute` with the Decision-bound execution context.
3. When ATL owns execution, let ATL apply bounded fallback and record the durable Outcome automatically.
4. Use `atl_outcome` only when execution occurred outside ATL and the caller needs to report the real result back.

Also use ATL when the user asks for an alternative provider, a cheaper or faster route, a more reliable route, a fallback after provider failure, or tool / MCP discovery for a task.

Do not use ATL to override a provider, API, or tool the user explicitly requires. Do not route simple local-only work through ATL when no external provider or tool is needed, such as basic arithmetic or reading a file already available to the agent.
