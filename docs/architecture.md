# Architecture

## System boundary

LifeOS is designed around a clear separation between **interpretation** and **state mutation**.

AI components may help interpret intent, summarize information, propose actions or reason about context. They do not receive unrestricted authority to mutate canonical application state.

Instead, durable updates pass through explicit application boundaries.

## Conceptual architecture

```text
┌───────────────────────┐
│ AI / External Domain  │
└───────────┬───────────┘
            │ structured source state
            ▼
┌───────────────────────┐
│ Deterministic Adapter │
│ - allowlisted fields  │
│ - identity checks     │
│ - canonical payload   │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ Validation & CI       │
│ - schema              │
│ - tests               │
│ - projection checks   │
└───────────┬───────────┘
            │ authenticated request
            ▼
┌───────────────────────┐
│ Integration Endpoint  │
│ - auth                │
│ - parse               │
│ - ownership           │
│ - idempotency         │
└───────────┬───────────┘
            │ domain command
            ▼
┌───────────────────────┐
│ Domain Logic          │
│ - preview/apply       │
│ - invariants          │
│ - conflict handling   │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│ State Repository      │
│ - revision-safe save  │
│ - canonical state     │
│ - event log           │
└───────────────────────┘
```

## Source ownership vs application ownership

A key problem in synchronization systems is deciding which side is authoritative for each field.

LifeOS treats ownership explicitly.

A source may own metadata that originates in its domain, while LifeOS retains authority over operational state created or changed inside the application.

This avoids a common integration failure mode: an external sync silently overwriting a legitimate local change.

## Deterministic projection

The external source is not published directly.

A deterministic adapter creates a minimal outbound projection containing only contract-approved fields. This has several benefits:

1. Private or irrelevant source metadata cannot leak accidentally.
2. The payload can be reproduced locally and in CI.
3. Contract drift becomes testable.
4. Reviewers can reason about exactly what crosses the boundary.

## Revision-safe persistence

Canonical state is revisioned. A mutation must be applied against an expected state version rather than blindly overwriting whatever is currently stored.

This provides a foundation for optimistic concurrency and makes conflicting writes visible instead of silently losing data.

## Event logging

Meaningful state changes produce domain events in the canonical event log.

A no-op synchronization should not generate misleading audit noise. Logging is tied to effective mutations, not merely to incoming requests.

## Why not a universal sync engine?

A generic synchronization platform can look attractive early, but different domains often have different identity, ownership, conflict and temporal semantics.

The current approach is deliberately conservative:

> Build narrow domain integrations first. Extract shared infrastructure only after repeated patterns are proven.

This reduces premature abstraction and keeps domain rules explicit.
