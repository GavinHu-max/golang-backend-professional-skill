# Durable history and operational observability

## Decide what each record proves

Use separate concepts even when one database serves them:

| Record | Identity and purpose | Completeness |
| --- | --- | --- |
| Inbound attempt | Server-generated request ID, received time, transport result | Every in-scope request, including invalid input and retries |
| Business operation | Stable domain/idempotency key, effective decision and state | One operation may have multiple attempts |
| Provider attempt | Attempt ID, provider correlation, timing and outcome | Includes timeout, rejected response, and uncertain completion |
| Audit event | Actor, action, resource/version, before/after or change summary | Durable evidence of changes |
| Diagnostic telemetry | Trace/span correlation and bounded attributes | May be sampled or expire; not the business ledger |

Draw the write points through success, no-op/bypass, validation failure, storage
failure, provider failure, timeout, cancellation, retry, and replay. A retry may
reuse the business result while still creating a new inbound-attempt record.
Distinguish 'not invoked' from 'invoked with unknown outcome' and 'provider denied'.
Missing historical data is unknown; do not backfill invented outcomes.

## Data model and access

Keep queryable dimensions typed: identifiers, tenant/scope, timestamps, state,
action, reason code, route/version, provider outcome, transport status, duration.
Store bounded, versioned snapshots separately from indexed summary columns. The
snapshot is evidence; it should not become a required JSON scan for every list.
Use real nulls for unknown values instead of misleading zero/false defaults.

Choose indexes from actual filters and ordering, considering write cost. Document
retention, approximate row/payload growth, and archive or deletion behavior. An
append-only event stream with a current-state projection is useful when ordered
transitions matter; a simple request row may be sufficient for a completed call.
Do not adopt event sourcing merely to display a timeline.

History reads must use existing authentication and server-side scopes. Restrict
large payloads and sensitive fields independently of list metadata if required.
Choose a fail-closed persistence requirement or explicitly bounded best-effort
capture based on the business requirement. Do not silently lose history while
claiming every request is recorded.

## Structured logs

Use stable event names and typed fields, for example request.received,
rule.evaluated, provider.completed, and request.completed. Include request_id,
operation_id, trace_id/span_id when actually present, service/environment, outcome,
duration, and stable error class. Do not fabricate standards-compliant trace IDs
from arbitrary caller strings; distinguish an application correlation ID.

Carry correlation in the original context. Emit one boundary completion record
per attempt with consistent timing and status. Log the error at the boundary that
owns handling rather than repeating full error payloads at every layer. Keep
metric label dimensions bounded; IDs and raw error text are unsuitable labels.

Define one redaction policy applied before all sinks, including database snapshots,
logs, exports, and UI responses. Prefer an allowlist for captured headers and
payload fields. Never store credentials or authentication material in general
history. Limit sizes/depth, distinguish redacted/truncated/invalid bodies, and avoid
mutating the live request when creating a redacted copy. If raw payload retention
is explicitly required, use separately authorized encrypted storage, narrow access,
and retention; general logs and a broadly accessible table are not a raw archive.

References: [OpenTelemetry log data model](https://opentelemetry.io/docs/specs/otel/logs/data-model/),
[Temporal event history](https://docs.temporal.io/workflow-execution/event),
[AWS Powertools logger](https://docs.aws.amazon.com/powertools/python/latest/core/logger/).
