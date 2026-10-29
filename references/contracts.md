# Contracts and consumer design

## Resource first, task first

Name list/get methods after stable resources, not individual UI tabs or commands.
Different commands may mutate the same resource. A create/disable approval can
share one change-record collection with an action discriminator and common state.
Do not flatten genuinely different authorization, lifecycle, or retention rules
solely to reduce endpoint count.

Separate the current resource, a requested change, and the history of changes.
One list endpoint can support status/action filters; a detail endpoint can return
timeline, requested snapshot, reviewer outcome, and application outcome. An
approval being accepted is not necessarily proof that asynchronous publication
finished. Document each state transition and permitted actions.

## List and detail shape

- Define stable identifiers, UTC timestamp format, optional-field semantics,
  typed status/action values, and the meaning of each filter.
- Bound page size, request time range, payload size, and query cost. Use deterministic
  ordering with an immutable tie-breaker. For growing history, a keyset cursor is
  often preferable to offset pagination; retain the existing style where compatible
  behavior matters more than a new mechanism.
- Treat cursors as opaque, validate their structure, bind them to the query
  constraints, and reapply authorization on every page. A cursor is never a grant.
- List compact metadata by default. Fetch large snapshots and timelines on detail.
  Do not run one extra database query per listed item.
- Put filters and scoped predicates in the database query before paging/counting;
  filtering afterward can leak counts or make pagination incorrect.
- Specify empty results, unknown resource, invalid filters, and inaccessible
  resource behavior. Preserve structured error codes across BFF boundaries.

## Compatibility and frontend handoff

Inventory direct RPC callers, BFF allowlists, generated types, and fixtures. Add
new resource methods with a migration path; keep old methods as adapters where a
consumer still needs them. Do not reuse a removed protobuf field number or silently
change the semantics of an existing field.

A frontend handoff should give exact method names, request/response examples,
status-to-label/action mapping, supported filters, pagination, detail loading,
loading/error/empty states, authorization behavior, and removal timing for old
calls. Distinguish JSON number representation from protobuf int64 string handling.
Provide executable contract examples when they add integration evidence.

References: [Google AIP-132](https://google.aip.dev/132),
[AIP-158](https://google.aip.dev/158), [AIP-180](https://google.aip.dev/180).
