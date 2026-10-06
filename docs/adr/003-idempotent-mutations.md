# ADR 003 — Idempotent, ownership-aware mutations

**Status:** Accepted

## Context

External publishing can be retried because of network failures, CI reruns or caller uncertainty. At the same time, an external source should not own every field in the receiving application.

Without explicit semantics, retries can duplicate effects and synchronization can overwrite legitimate local changes.

## Decision

Domain mutations are designed around:

1. stable external identity;
2. explicit field ownership;
3. idempotent operation identifiers;
4. revision-aware persistence;
5. event creation only for effective changes.

## Consequences

### Positive

- Retries are safer.
- Local operational changes are protected.
- Duplicate side effects are reduced.
- Audit history reflects actual mutations.
- Concurrency conflicts become visible.

### Trade-offs

- Identity and ownership semantics must be defined per domain.
- Idempotency does not automatically solve event ordering.
- More domain logic is required than in a generic upsert endpoint.

## Principle

> An integration is not permission to overwrite state. It is a constrained domain operation.
