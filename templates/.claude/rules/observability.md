# Observability

Logs, metrics, and traces are how the agent debugs production code it cannot rerun. They are also how the *next* agent debugs the bug you didn't catch. Treat instrumentation as a first-class part of the change, not an afterthought.

## IRON LAW

**NO PII OR SECRETS IN LOGS, METRICS, OR TRACES.**

Passwords, tokens, API keys, full credit card numbers, full SSNs, raw auth headers — never logged, never tagged on a span, never in a metric label. If a value is sensitive in the database, it is sensitive in the log line. Mask, hash, or omit.

## GOLDEN RULES

- **Aim for structured logs.** Key-value pairs (or JSON), not f-string prose. Future-you grepping prod is the audience.
- **Aim for one event per logical operation, not one log per line of code.** Logs are a story, not a transcript.
- **Aim for correlation.** Every log within a request, job, or trace carries the same correlation id. A stack of unconnected logs is noise.
- **Aim for severity that means something.** `ERROR` pages someone. `WARN` is anomalous-but-handled. `INFO` is the narrative. `DEBUG` is dev-only.

## What to log

- **Boundary crossings** — request received, request sent to downstream, response received. With latency and status.
- **Decision points where a value depends on data** — which branch was taken, which resource was selected.
- **Errors with context** — the operation, the inputs (redacted), the upstream cause.
- **Rare-but-important events** — feature flag flipped, fallback path engaged, retry exhausted.

## What NOT to log

- Per-iteration loop bodies. If you need this for debugging, gate behind `DEBUG`.
- Full request/response bodies by default. Sample, redact, or omit.
- Stack traces for expected control flow (e.g., a 404 is not an error event).
- Anything you would not want to see screenshotted in a postmortem.

## Metrics

- **Counters** for *what happened* (`requests_total`, `errors_total`).
- **Histograms** for *how long / how big* (`request_duration_seconds`).
- **Gauges** for *what's true now* (`queue_depth`, `connections_active`).
- **Cardinality discipline:** never label a metric with a high-cardinality value (user id, request id, full URL). Aggregations break.

## Tracing

- Spans wrap units of work; nest them to show causality.
- Tag spans with operation name, status, and known dimensions (route, db query type). Not with user-identifiable data.
- A trace that doesn't tell you where time went is a span graph that's too coarse — split.

## Project-Specific Notes

{{ OBSERVABILITY_NOTES }}
