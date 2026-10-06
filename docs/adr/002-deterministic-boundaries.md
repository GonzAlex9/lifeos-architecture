# ADR 002 — Deterministic integration boundaries

**Status:** Accepted

## Context

Modern AI systems can generate and transform structured data, but not every transformation benefits from probabilistic reasoning.

Using an LLM for objective projection logic makes behavior harder to test, reproduce and audit.

## Decision

Objective transformations across integration boundaries are implemented deterministically.

The adapter:

- selects allowlisted fields;
- checks required identity;
- rejects unsupported shapes;
- emits a reproducible canonical payload.

AI may contribute before or after this boundary when interpretation is actually required.

## Consequences

### Positive

- Reproducible payloads.
- Simple unit testing.
- Lower cost and latency.
- Easier security review.
- Smaller failure surface.

### Trade-offs

- Contract changes require explicit code changes.
- Domain schemas must be maintained.

## Principle

> Use models for ambiguity. Use code for invariants.
