# Reliability and storage changes

## Persistence and external effects

Represent a business operation's state machine explicitly. An HTTP timeout does
not prove the provider did nothing. Keep provider request identifiers and an
uncertain/reconciliation state when completion cannot be known safely. Never
blindly retry a non-idempotent external mutation.

Use a database uniqueness constraint for the chosen idempotency scope, not a
read-then-insert race. Define the canonical input digest, request conflicts,
concurrent in-progress behavior, replay response, and recovery of abandoned work.
Do not include newly generated timestamps in a digest meant to identify the same
business input. A uniqueness collision is not automatically a successful replay.

Keep transactions short. Do not hold a database transaction open across a slow
provider call merely for apparent atomicity. Record intent and state transitions;
use an outbox and idempotent consumer if a committed change must publish elsewhere.
Specify the consistency lag and reconciliation path. A goroutine is not a durable
queue, and exactly-once delivery must not be claimed without the full mechanism.

## Context, lifecycle, and overload

Keep cancellation and deadlines through the call tree. Give retries a shared total
budget, bounded attempts, backoff/jitter where appropriate, and a classification of
retryable failures. Set transport and storage pool limits. Do not retry validation
or authorization failures.

When history must be finalized after a client disconnect, narrowly detach that
specific finalization using the language's context-preserving mechanism and an
explicit short deadline. Do not replace normal work with a background context.
Test the shutdown path, drain order, finalization deadline, and failure visibility.

## Schema rollout

Inspect existing rows, constraints, query patterns, and migration tooling before
designing a change. Draft reviewed migration SQL and a fresh-install schema from
the same intended model. Prefer additive expand/contract rollouts when old and new
versions can overlap. State backfill semantics and nullability honestly.

Check index availability, uniqueness conflicts, data conversion, lock/rebuild cost,
and rollback compatibility. Do not run a migration on a shared database merely
because generating it was authorized. Validate fresh install and upgrade on an
isolated database using representative records; destructive rollback validation
also belongs in an isolated environment.

## Evidence worth collecting

- Positive and negative business decisions, including intentional bypass.
- Invalid input and authentication/authorization denial without data leakage.
- Concurrent duplicates, changed input under the same idempotency key, replay.
- Provider rejection, transport failure, timeout, malformed payload, cancellation.
- Persistence failure before and after an external effect.
- Stable pagination under identical timestamps and concurrent inserts.
- Cross-scope list/detail access, snapshot masking, oversized payload behavior.
- Fresh schema, upgrade from existing schema, startup compatibility and shutdown.

Choose the checks relevant to the actual change; this list is not a requirement to
build an unrelated resilience platform.
