# ADR 001 — Durable state over conversational memory

**Status:** Accepted

## Context

AI conversations are transient execution environments. Important project state can outlive a model session, chat history or UI surface.

Depending exclusively on conversational memory makes recovery, auditability and deterministic continuation difficult.

## Decision

State that must survive across sessions is represented explicitly in durable, versionable structures.

Conversation history may inform reasoning, but it is not the authoritative source of operational state.

## Consequences

### Positive

- State can be inspected without replaying conversations.
- New sessions can recover from explicit checkpoints.
- Changes can be reviewed and versioned.
- Deterministic components can operate without model memory.

### Trade-offs

- More explicit state modeling is required.
- State schemas and ownership rules must be maintained.
- Not every conversational detail should be persisted.

## Principle

> If losing the conversation would break the system, that information belongs outside the conversation.
