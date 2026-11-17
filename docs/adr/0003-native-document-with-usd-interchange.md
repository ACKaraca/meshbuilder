# 0003. Native document as authority, OpenUSD as assembly and interchange

- Status: Accepted
- Capabilities: MB-SCENE-028, MB-USD-001, MB-USD-011, MB-IO-012, MB-RENDER-007

## Context

OpenUSD is the scene-description standard of content pipelines, and MeshBuilder treats it as native
vocabulary. USD describes composed results well but not editable history: modifier stacks, geometry
graphs, rig logic, simulation setups and identity-keyed edits. The suite stores every document in the
Arc document package, which provides transactions, an operation journal, recovery and partial loading.

## Decision

The `.meshb` Arc document package is the authority for everything MeshBuilder edits. Native concepts
are defined so that they map directly onto USD concepts (prims, primvars, subdivision schemes,
instancing, variant sets, `UsdSkel`, `UsdLux`, MaterialX materials). USD stages are hosted in the
document as stage objects and edited in place through commands that write opinions to the chosen edit
target. Native content is exported to USD as evaluated results; native editing of USD content converts
a prim subtree to native objects and writes the result back. The document uses metres and Z-up and
writes `metersPerUnit` and `upAxis` accordingly. Rendering goes through the Arc Scene contract, which
is aligned with USD. The mapping is specified in [scene-and-usd.md](../architecture/scene-and-usd.md).

## Consequences

- Native editing keeps full history, journaling and recovery; USD layers stay free of
  application-specific history.
- Stage editing is journaled and undoable like native editing.
- Editable history does not travel through USD; collaborators who need it exchange `.meshb` documents
  ([OQ-09](../product/open-questions.md)).
- Two representations are kept consistent by mapping tests in CI.

## Alternatives considered

- **USD as the native file format.** Removes a mapping layer, but leaves no place for editable history
  and conflicts with the suite's transactional package.
- **USD only as an export format.** Simpler, but fails pipelines that assemble and edit shots in USD.

## Reopen when

USD gains standard schemas for procedural history that cover modifier stacks and node graphs, or
pipeline partners require history to travel inside USD layers.
