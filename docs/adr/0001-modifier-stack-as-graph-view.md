# 0001. The modifier stack is a view of the geometry graph

- Status: Accepted
- Capabilities: MB-MOD-001, MB-MOD-008, MB-GRAPH-029, MB-SCENE-036

## Context

Users need two ways to build non-destructive geometry: an ordered list of operations on an object,
which is quick to read and reorder, and a general node graph, which can branch, merge several inputs
and build reusable assets. Maintaining two evaluation systems would duplicate every operation, split
caching and profiling, and make results depend on which editor was used. Rigging, simulation and
motion graphics also need a procedural system, and building a separate one for each would repeat the
problem.

## Decision

Each geometric object's evaluated data is produced by one geometry graph on `arc-nodes`. The modifier
stack editor is a projection of that graph: when the graph is a linear chain from base data to
output, the stack shows each node as a modifier; when the graph branches, the stack shows the
branching part as one graph entry. Every geometry operation is implemented once in
`meshbuilder-model` and exposed as a command, a modifier and a node. Deformers, rig nodes and
simulation zones are nodes in the same graph ([evaluation](../architecture/evaluation.md)).

## Consequences

- One implementation, one cache, one profiler and one determinism rule for all procedural work.
- Stack users never see graph concepts unless they open the graph; graph users can see any stack as
  a graph.
- Operations must be pure functions of inputs and parameters with declared reads, so interactive
  tools preview through the evaluation graph.
- The stack editor must handle graphs edited into non-linear shapes gracefully.

## Alternatives considered

- **Separate stack and graph engines.** Familiar, but doubles implementation and produces
  inconsistent results between editors.
- **Graph only.** Maximally general, but slows routine modeling where an ordered list is clearer.
- **Construction history attached to shapes.** Hides structure and becomes fragile as history grows;
  replaced here by explicit graphs with stable component identity.

## Reopen when

A class of operation cannot be expressed as a pure graph node without unacceptable performance, or
stack users consistently cannot work with graph-produced objects.
