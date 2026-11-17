# 0005. Time as an evaluation input and a revision-aware frame cache

- Status: Accepted
- Capabilities: MB-SCENE-038, MB-SCENE-040, MB-PROJ-010, MB-GRAPH-026

## Context

Animated scenes evaluate the same graph at many times. Re-evaluating everything on each frame wastes
work on static content, and caching frames is only safe when every cached result can be invalidated
precisely after an edit. Frame rates include fractional rates, and floating-point time drifts over
long sequences and simulation substeps.

## Decision

Time is an explicit input node of the evaluation graph; nodes declare time dependency, and only
time-dependent nodes and their dependents re-evaluate when the frame changes. Time is stored as
integer ticks at 705,600,000 per second, which divides all common integer and fractional frame rates.
Evaluated results are cached per object and frame in a memory-budgeted frame cache keyed by the
revision of their inputs; edits invalidate only affected objects and frame ranges; a background
filler populates the cache during idle time and playback. Simulation zones keep per-frame state in a
separate state cache. Details are in the [evaluation model](../architecture/evaluation.md).

## Consequences

- Playback from a filled cache runs at the scene frame rate regardless of rig complexity.
- Static content costs nothing per frame.
- Nodes must declare time dependency correctly; a missing declaration shows stale results, so node
  tests check it.
- Memory use is bounded by a user-visible budget, and the timeline shows cache state.

## Alternatives considered

- **Floating-point seconds.** Simple, but drifts with fractional rates and long ranges.
- **Whole-scene frame snapshots.** Easy invalidation, but large memory and no reuse of static data.
- **No cache, faster evaluation only.** Insufficient for heavy rigs and simulations.

## Reopen when

The tick resolution proves insufficient for required sub-frame sampling
([OQ-17](../product/open-questions.md)), or cache memory needs cannot be met without disk spill
([OQ-16](../product/open-questions.md)).
