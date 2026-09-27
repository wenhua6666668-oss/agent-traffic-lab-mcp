# ATL Plugin Review Test Cases

These cases are intended for directory review and manual verification of the ATL plugin behavior.

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
