# Security & Reliability

AI-enabled integrations increase the importance of explicit trust boundaries.

This architecture treats external publishers as constrained callers rather than trusted writers.

## Authentication

Structured publishing uses authenticated server-to-server requests. Secrets remain outside source state, generated payloads, frontend code and documentation.

## Ownership resolution

An external caller cannot freely choose the owner of the state it wants to mutate. Ownership is resolved by trusted server-side configuration or identity rules.

This prevents a valid integration credential from becoming a generic cross-owner write capability.

## Payload minimization

Outbound payloads are generated through an allowlist rather than by forwarding arbitrary source objects.

This reduces accidental data exposure and prevents undeclared fields from becoming implicit API behavior.

## Schema validation

The public contract defines:

- required fields;
- supported values;
- maximum sizes;
- allowed object shape;
- rejection of unknown properties.

Strict contracts make both human review and automated testing easier.

## Idempotency

Publish operations carry a stable operation identifier so retries can be recognized.

Idempotency protects against duplicate effects, but it is not treated as a complete ordering mechanism. A unique operation identifier does not by itself prove that an event is newer than another event.

## Concurrency

Persistence uses revision-aware writes. If canonical state changes between read and save, the mutation must be reconsidered instead of silently overwriting newer state.

## No-op behavior

A synchronization that produces no effective change should remain a no-op:

- no duplicate mutation;
- no unnecessary revision churn;
- no misleading audit event.

## AI boundary

LLMs are not used to perform deterministic transformations that can be expressed as code.

This reduces variability in:

- identity mapping;
- payload generation;
- validation;
- ownership enforcement;
- persistence rules.

AI remains valuable for interpretation and reasoning, while deterministic software remains responsible for objective invariants.
