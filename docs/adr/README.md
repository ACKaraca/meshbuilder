# Architecture decision records

Decisions specific to MeshBuilder. Suite-wide decisions are recorded in the
[suite ADRs](https://github.com/ACKaraca/arc-suite/tree/main/docs/adr) and are not repeated here.

## Index

| ADR | Title | Status |
|---|---|---|
| [0001](0001-modifier-stack-as-graph-view.md) | The modifier stack is a view of the geometry graph | Accepted |
| [0002](0002-stable-component-identity.md) | Stable component identity for non-destructive editing | Accepted |
| [0003](0003-native-document-with-usd-interchange.md) | Native document as authority, OpenUSD as assembly and interchange | Accepted |
| [0004](0004-fbx-through-isolated-bridge.md) | FBX through an isolated bridge process | Accepted |
| [0005](0005-time-as-evaluation-input-and-frame-cache.md) | Time as an evaluation input and a revision-aware frame cache | Accepted |
| [0006](0006-dense-geometry-as-chunked-assets.md) | Dense geometry and sculpt data as chunked package assets | Accepted |

## Template

```markdown
# NNNN. Title

- Status: Proposed | Accepted | Superseded by NNNN
- Capabilities: MB-AREA-NNN, ...

## Context

The forces at play, stated neutrally.

## Decision

What is decided, in present tense.

## Consequences

What becomes easier, what becomes harder, what must now be done.

## Alternatives considered

Each option with the reason it was not chosen.

## Reopen when

The observable trigger that would justify revisiting the decision.
```
