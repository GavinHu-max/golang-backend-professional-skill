---
name: golang-backend-professional
description: Design, implement, or review Go backend architecture across API contracts, persistence, request history, observability, and external-service reliability. Use for substantial backend changes or architecture reviews; preserve the project's stack and avoid unrelated rewrites.
---

# Professional Go backend architecture

Turn a business requirement into a small, testable change with explicit contracts,
failure behavior, and operational evidence. This skill supplies architectural
decision criteria, not a framework template or permission to deploy.

## Establish the actual system

Read project instructions and the relevant business requirements, then inspect the
current code, migrations, generated-contract workflow, callers, and tests. When a
UI or BFF consumes the API, inspect its actual queries and displayed fields.
Distinguish desired behavior from deployed behavior and stale documentation.

Follow explicit user decisions and project conventions. Reuse the configured
logger, database layer, validation, configuration, and dependency injection before
introducing replacements. Prefer the smallest design that satisfies the current
requirements; a familiar enterprise stack is not evidence that it is needed.

For consequential design choices, state:

- The invariant or user-visible problem.
- The owning component and data contract.
- Failure and retry behavior, including partially completed operations.
- How to demonstrate correctness and what remains unverified.

Use source-backed examples when an unfamiliar design needs evidence. Start with
maintained official repositories and documentation; stars help discover candidates
but do not establish correctness or fit. Read
[reference patterns](references/reference-patterns.md) for selected sources and
their limits. Refresh changeable APIs, versions, maintenance, and popularity before
presenting them as current facts.

## Choose the relevant work

- **APIs or BFF integration:** read [contracts](references/contracts.md). Design
  around resources and caller tasks; avoid making consumers reconstruct domain
  state from unrelated transport endpoints.
- **Decision history, auditing, logging, or database records:** read
  [history and observability](references/history-observability.md). Model durable
  business evidence independently of diagnostic telemetry.
- **External effects, retries, concurrency, or migrations:** read
  [reliability](references/reliability.md). Make ambiguous outcomes explicit.

Load only relevant references. Use installed language/tool skills when their
specific mechanics are needed; this skill must remain useful without them.

## Implement and prove the change

Keep transport validation/mapping, application orchestration, business rules, and
data access responsibilities explicit within the existing layout. Do not create
packages or interfaces that have no current boundary or consumer. Propagate the
original context through database and external calls, and bound expensive work.

Map affected callers before changing an exported contract. Keep behavior changes
reviewable separately from unrelated cleanup. Preserve protobuf field numbers;
generate code using the project's toolchain. Update consumer examples and migration
instructions with the implementation.

Test the business invariant, including its negative case. Exercise realistic
storage constraints and concurrency where they affect correctness. Fakes validate
orchestration; local services validate integration; neither proves a live provider
or deployment. Do not add tests that merely repeat constants or implementation text.

Inspect affected runtime packaging, ports, startup/shutdown timing, secrets, schema
rollout, and rollback compatibility. Use existing CI checks. Stop repeating passing
checks unless new changes or unresolved concerns justify them.

Deliver the changed behavior and contract, evidence actually obtained, migration
and consumer steps, and material limits. Commit, push, publish, deploy, or alter
shared infrastructure only within the user's authorization. A completed local
implementation and a successful rollout are separate claims.
