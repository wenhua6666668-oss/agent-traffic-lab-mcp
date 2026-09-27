# ATL Plugin Review Materials

These materials are intended for OpenAI Plugin Directory review and manual verification of Agent Traffic Lab (ATL).

## Positive cases

1. **Search**
   - User: `Search for the latest critical CVE.`
   - Expected: ATL is eligible to auto-trigger. The agent should call `atl_decide` with the task, then follow a successful Decision's `next_step` to `atl_execute`.

2. **Extract**
   - User: `Extract the product name, price, and availability from this page.`
   - Expected: ATL may infer `extract`, call `atl_decide`, then `atl_execute` if ATL owns execution.

3. **Summarize**
   - User: `Summarize this support ticket for handoff.`
   - Expected: ATL may infer `summarize`, call `atl_decide`, then `atl_execute`.

4. **Translate**
   - User: `Translate this paragraph into Spanish.`
   - Expected: ATL may infer `translate`, call `atl_decide`, then `atl_execute`.

5. **Classify / fallback routing**
   - User: `Classify this customer message and use a reliable alternative if the first provider is unavailable.`
   - Expected: ATL may infer `classify`; routing may consider reliability and bounded fallback.

## Negative cases

1. **Simple arithmetic**
   - User: `Calculate 19 * 27.`
   - Expected: Do not use ATL. This does not need provider discovery or routed execution.

2. **Local file read**
   - User: `Read this local file from my computer.`
   - Expected: Do not use ATL solely to access a local file. Use the host's local-file capability if available.

3. **Already-selected direct API execution**
   - User: `Call this already-selected API directly with these credentials.`
   - Expected: Do not route through ATL merely to re-select a provider. Use the already-selected integration, and never expose credentials to ATL unless explicitly appropriate.

## Starter prompts

- `Search for the latest critical CVE.`
- `Find and execute a reliable provider to summarize this text.`
- `Extract structured fields from this page.`
- `Translate this into Spanish using an eligible provider.`
- `Classify this support ticket and use bounded fallback if needed.`

## Tool annotation justifications

### `atl_decide`
- `readOnlyHint: true` — the tool computes and returns a routing Decision. It does not itself execute the selected provider or mutate user data.
- `destructiveHint: false` — it does not delete, overwrite, send, revoke, or otherwise cause an irreversible user-facing action.
- `openWorldHint: true` — routing may inspect open-ended provider/tool/MCP availability and eligibility outside a bounded private workspace.

### `atl_execute`
- `readOnlyHint: false` — the tool executes the provider bound to an ATL Decision and records the resulting Outcome, so it changes execution state and can trigger external provider work.
- `destructiveHint: false` — ATL's supported V1 execution surface is limited to search, extract, summarize, translate, and classify; it does not delete or overwrite user data, send messages, make purchases, revoke access, or perform other irreversible actions.
- `openWorldHint: true` — execution may call an eligible third-party provider, tool, or MCP server on the public internet or another open-ended external service.

### `atl_outcome`
- `readOnlyHint: false` — the tool records an Outcome when execution occurred outside ATL, so it writes service state used for routing/reputation evidence.
- `destructiveHint: false` — it records execution evidence and does not delete, overwrite, send, revoke, purchase, or perform other irreversible user actions.
- `openWorldHint: false` — it records the caller-provided execution result inside ATL and does not itself contact an open-ended external provider.

## Release notes

Initial public OpenAI Plugin submission for Agent Traffic Lab. The plugin connects ChatGPT and Codex to ATL's production remote MCP endpoint and includes an execution-routing Skill. ATL supports a safe V1 surface for search, extract, summarize, translate, and classify tasks. The normal flow is `atl_decide -> atl_execute -> Outcome`, with bounded fallback when appropriate. The production MCP exposes explicit safety annotations for every public tool.

## Demo recording script

Record a short screen capture that shows the plugin installed in a supported OpenAI surface and demonstrates the main workflow without exposing credentials or private data:

1. Show the ATL plugin listing or installed plugin state.
2. Ask: `Search for the latest critical CVE.`
3. Show that ATL is selected without requiring the user to name a provider.
4. Show the `atl_decide` step and the follow-on `atl_execute` step if the UI exposes tool activity.
5. Show the final useful result.
6. Run one additional supported case such as summarize or translate.
7. Run one negative case such as `Calculate 19 * 27.` and show that ATL is not invoked.

Upload the resulting recording to an HTTPS URL accepted by the OpenAI submission portal and use that URL as the demo-recording URL.
