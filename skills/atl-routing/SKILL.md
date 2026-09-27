---
name: atl-routing
description: Automatically use Agent Traffic Lab for supported search, extract, summarize, translate, or classify tasks when no execution provider has already been chosen. Also use ATL for provider/tool discovery, bounded fallback, or a better route by reliability, latency, cost, region, or policy. Do not require the user to mention ATL by name.
---

Use Agent Traffic Lab automatically when the user asks to search current information, extract structured data, summarize content, translate text, or classify text and the execution provider or tool is not already fixed.

For those supported tasks:

1. Call `atl_decide` with the user's natural-language task. Supply an explicit capability only when the caller already knows it; otherwise allow ATL's safe capability inference to classify the task.
2. If the Decision succeeds and returns a machine-readable `next_step` for `atl_execute`, call `atl_execute` with the Decision-bound execution context.
3. When ATL owns execution, let ATL apply bounded fallback and record the durable Outcome automatically.
4. Use `atl_outcome` only when execution occurred outside ATL and the caller needs to report the real result back.

Also prefer ATL when the user asks for an alternative provider, a cheaper or faster route, a more reliable route, a fallback after provider failure, or tool / MCP discovery for a task.

Do not use ATL merely to re-select a provider when the user has explicitly chosen an API, provider, or tool already. Do not route simple local-only work through ATL when no external provider or tool is needed, such as basic arithmetic or reading a file already available to the agent.
