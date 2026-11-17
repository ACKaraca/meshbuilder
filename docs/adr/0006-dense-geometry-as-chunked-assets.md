# 0006. Dense geometry and sculpt data as chunked package assets

- Status: Accepted
- Capabilities: MB-SCENE-028, MB-SCENE-029, MB-SCENE-030

## Context

MeshBuilder documents hold meshes with tens of millions of faces, multiresolution sculpt levels,
sculpt layers and high-resolution paint layers. Storing such data as one object payload makes every
save rewrite gigabytes, makes undo copy whole meshes and makes opening a document wait for all of it.
The Arc document package supports content-addressed assets and partial loading.

## Decision

Large geometric data (base meshes above a size threshold, multiresolution levels, sculpt layer
deltas, paint layer tiles, baked caches) is stored as fixed-size chunks in the package's
content-addressed asset table. Object payloads hold chunk lists, not the data. Edits replace only the
chunks they touch; the journal records chunk replacements, so undo and recovery store deltas.
Unchanged chunks are shared between revisions and between objects. Chunks load lazily when a view or
an evaluation needs them.

## Consequences

- Saves write only changed chunks; documents with dense data open to a first view quickly.
- Undo memory for sculpt strokes is proportional to the touched area.
- Chunk layout is stable and documented as part of the package schema.
- Tools that read the package directly must understand chunk lists.

## Alternatives considered

- **Whole payload per object.** Simple, but slow to save, undo and open for dense data.
- **External sidecar files.** Fast, but breaks the single-file document and atomic saves.

## Reopen when

Measured save, open or undo costs for reference documents exceed the targets in the
[architecture overview](../architecture/overview.md).
