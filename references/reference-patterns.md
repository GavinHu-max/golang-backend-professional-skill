# Reference patterns and limits

These are discovery starting points, not endorsements of a particular stack.
Read the relevant implementation and verify current maintenance and licenses when
adopting code. The guidance in this skill is original synthesis; no upstream code
is bundled. Repository popularity below is an approximate public-page snapshot
observed on 2026-10-29 and will change.

| Source | Snapshot | Evidence read | Transferable pattern | Boundary |
| --- | --- | --- | --- | --- |
| [Uber Zap](https://github.com/uber-go/zap) | 24.7k stars | [logger.go](https://github.com/uber-go/zap/blob/master/logger.go) | Typed structured fields and reusable logger context | Keep a project's existing logger adapter; a logging library is not a durable audit database |
| [OpenTelemetry Go](https://github.com/open-telemetry/opentelemetry-go) | 6.6k stars | [log/record.go](https://github.com/open-telemetry/opentelemetry-go/blob/main/log/record.go), [log data model](https://opentelemetry.io/docs/specs/otel/logs/data-model/) | Explicit event name, timestamps, severity, body, attributes and trace correlation | Telemetry export/sampling does not establish complete business history |
| [Temporal](https://github.com/temporalio/temporal) | 24k stars | [typed log tags](https://github.com/temporalio/temporal/blob/main/common/log/tag/tags.go), [event history](https://docs.temporal.io/workflow-execution/event) | Separate execution identity, transitions and durable history | Its event history supports an execution engine; a request-history feature rarely needs that engine |
| [AWS Powertools Python](https://github.com/aws-powertools/powertools-lambda-python) | 3.3k stars | [Logger](https://docs.aws.amazon.com/powertools/python/latest/core/logger/) | Consistent service/correlation fields; incoming payload logging disabled by default for sensitive-data protection | Python/Lambda implementation is a pattern reference, not a Go/container dependency recommendation |
| [Google APIs](https://github.com/googleapis/googleapis) | Not recorded | [operations.proto](https://github.com/googleapis/googleapis/blob/master/google/longrunning/operations.proto), [AIP-132](https://google.aip.dev/132), [AIP-158](https://google.aip.dev/158), [AIP-180](https://google.aip.dev/180) | Consistent resource list/get shape, explicit state/results, bounded pagination and compatibility | Long-running Operation is an example; do not force synchronous endpoints into an asynchronous contract |

When applying a pattern, leave a short decision record: source path and inspected
revision/date, invariant borrowed, fit with the actual workload, behavior deliberately
not copied, and validation evidence. Attribute derived recommendations as design
inferences rather than claims that a source prescribes the exact local schema.

Do not use organization brand or star counts as an architectural proof. Compare
the source's scale, trust boundaries, consistency requirements, and lifecycle with
the target system before borrowing its design.
