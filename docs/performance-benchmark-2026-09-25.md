# ATL production routing benchmark — 2026-09-25

This document records a real external benchmark against the production ATL Decision endpoint after the first Decision hot-path performance pass.

## What was tested

Benchmark source: GitHub-hosted Ubuntu runner calling the public production endpoint:

`https://mcp.agenttrafficlab.com/intent-entrance/decision`

The benchmark used fresh request IDs for every call. It tested:

- 12 `summarize` requests with varied English, Chinese, and Spanish task text.
- 4 unsupported capability requests: `image_generation`, `embeddings`, `speech_to_text`, and `translation`.
- Full versus compact Decision response payload size.

The supported-route expectation was the currently verified production route:

- Primary: `real:groq/openai/gpt-oss-20b`
- Fallback: `real:openrouter/liquid/lfm-2.5-2.6b:free`

## Results

| Metric | Result |
|---|---:|
| Supported requests routed as expected | 12 / 12 |
| Unsupported requests rejected with `NO_MATCH` | 4 / 4 |
| External mean latency | 2169.8 ms |
| External median latency | 1751.5 ms |
| External P95 latency | 3416.0 ms |
| External fastest request | 1660.3 ms |
| External slowest request | 3491.7 ms |
| Full response size | 9033 bytes |
| Compact response size | 783 bytes |
| Response-byte reduction using compact mode | 91.3% |
| Internal Decision latency observed in comparison request | ~1.0 ms |

## What the numbers mean

The internal Decision timing is the time ATL reports for the routing decision itself. It does **not** include all internet, TLS, proxy, platform, queueing, or response-transfer overhead.

The external latency is end-to-end from a GitHub-hosted runner to the public production endpoint and back. It is therefore a more realistic integration measurement, but it is also affected by runner region and network conditions.

The 91.3% reduction applies to the serialized ATL Decision **response body** in this benchmark when `?compact=1` is used. It should not be described as a 91.3% reduction in total application bandwidth, model-token usage, provider cost, or end-to-end traffic.

The 12/12 and 4/4 figures are benchmark observations for this test set, not a claim of universal routing accuracy. ATL currently has a much narrower set of fully verified production capabilities than a mature multi-provider routing network.

## Why compact mode exists

The canonical full Decision object is retained for compatibility and diagnostics. For machine-to-machine routing where the caller mainly needs the selected provider, fallback, authorization/reference information, and compact metadata, append:

`?compact=1`

This keeps the routing contract useful while removing verbose evidence and diagnostic material from the wire response.

## Reproducibility

The benchmark was executed after deployment of the Decision hot-path change merged in PR #12. The benchmark was run externally rather than inside the ATL process so it measured the public production path.

Future benchmark reports should record the date, source region where known, request count, supported capability set, production route expectation, and exact payload mode. Results should be updated rather than presented as timeless guarantees.
