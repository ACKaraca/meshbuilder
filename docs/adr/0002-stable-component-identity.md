# 0002. Stable component identity for non-destructive editing

- Status: Accepted
- Capabilities: MB-MOD-006, MB-SCENE-024, MB-ASSET-002, MB-ATTR-019, MB-UV-025

## Context

Non-destructive modeling breaks when a downstream edit refers to vertices, edges or faces by index:
any upstream change that alters topology shifts indices, and the edit lands on the wrong elements or
fails. Selection sets, recorded edits, reference overrides, attribute copies and blend-shape deltas
all need a way to address components that survives upstream changes.

## Decision

Every vertex, edge and face carries a 64-bit identifier unique within its geometry. Identifiers are
derived deterministically: an operation that creates components derives each new identifier from the
operation's node identifier and the identifiers of the input components it was created from, plus an
ordinal for one-to-many cases. Components that pass through an operation unchanged keep their
identifier. Everything that refers to components stores identifiers, never indices. When a referenced
component no longer exists, the referring item reports the missing identifiers instead of guessing.

## Consequences

- Recorded edits, selection sets and overrides survive upstream parameter changes for every component
  that still exists.
- Every operation implements identity propagation rules, and tests check them.
- Memory grows by one 64-bit value per component plus lookup tables built on demand; the budget is at
  most 15 percent of mesh memory, measured in CI ([OQ-02](../product/open-questions.md)).
- Reproducible identifiers depend on deterministic operations, which the
  [evaluation model](../architecture/evaluation.md) already requires.

## Alternatives considered

- **Index-based references.** Simple and compact, but fragile under any upstream topology change.
- **Geometric matching on re-evaluation.** Tolerant, but ambiguous on symmetric or coincident
  geometry and expensive on dense meshes.
- **Random identifiers per component.** Unique, but not reproducible across evaluations, which breaks
  determinism and caching.

## Reopen when

Measured overhead exceeds the budget on reference meshes, or identity rules for a required operation
cannot be defined.
