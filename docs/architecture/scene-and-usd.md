# Native scene model and OpenUSD

What a `.meshb` document stores natively, how OpenUSD stages, layers, variants and references map to
and from it, how materials exchange as MaterialX with OpenPBR, and how the scene reaches RenderBuilder
through the Arc Scene contract. The decision behind this split is
[ADR-0003](../adr/0003-native-document-with-usd-interchange.md).

## Two representations, one vocabulary

The native document is the authority for everything MeshBuilder edits: it holds editable history
(modifier stacks, geometry graphs, rigs, simulation setups, recorded edits keyed by component
identifiers) that USD does not describe. USD is the assembly and interchange layer: MeshBuilder hosts
USD stages directly, edits them in place, and publishes native content as USD.

Native concepts are named and structured so they map onto USD without invention:

| Native concept | USD concept |
|---|---|
| Scene object hierarchy | Prim hierarchy (`Xform` and typed prims) |
| Transform channels | `xformOp` stack (translate, rotate with order, scale; pivots as paired ops) |
| Mesh with subdivision settings | `Mesh` with `subdivisionScheme`, creases, corners, holes, interpolation rules |
| Attributes | Primvars with interpolation (constant, uniform, vertex, faceVarying) |
| Curve set | `BasisCurves` or `NurbsCurves` |
| Points | `Points` |
| Volume | `Volume` with `OpenVDBAsset` fields |
| Instance / collection instance | Instanceable prims or `PointInstancer` |
| Material (OpenPBR surface graph) | `Material` with MaterialX network |
| Lights, environment | `UsdLux` lights, `DomeLight` |
| Camera | `Camera` with physical attributes |
| Material and object variants | Variant sets |
| Layer (display) | Not a USD concept; written as custom metadata only |
| Collection | `UsdCollectionAPI` where used for bindings or render setups |
| Skeleton, skin binding, blend shapes | `UsdSkel` |
| Animation | Time samples (baked); curve data preserved as custom metadata |
| Document unit, up axis | `metersPerUnit` (1.0), `upAxis` (Z) |

## What is stored natively

| Stored in `.meshb` | Not stored (derived) |
|---|---|
| Objects, base data, attributes, component identifiers | Evaluated geometry |
| Modifier stacks, geometry graphs, graph assets | Frame cache, node caches |
| Materials as node graphs, texture assets (embedded or referenced) | Compiled shaders, thumbnails (stored but regenerable) |
| Rigs, skin weights, blend-shape deltas, animation curves, clips, layers | Baked animation for export |
| Sculpt levels and layers, paint layers as chunked assets | Flattened textures for export |
| Stage objects: root layer path, session layer content, layer stack state, edit target, load set | The composed stage |
| Document references with overrides, Arc Scene links with refinements | Referenced content |
| Preserved foreign data from imports | |

## Hosting stages

- A stage object opens a USD root layer through the USD library behind `arc-io-usd` and displays
  the composed stage in the viewport without converting prims to native objects.
- The session layer holds MeshBuilder's non-persistent display state and is saved in the document,
  not in the user's layers.
- Edits to stage prims (transform, visibility, variant selection, material binding, attribute
  values) are written as opinions into the current edit target layer through commands, so they are
  undoable and journaled like native edits; the journal records layer, prim path and before/after
  values.
- Payloads load on demand; population masks restrict composition to selected paths.
- Stage time maps to scene ticks with an offset and scale per stage object.

## Native edit and write-back

1. The user selects a prim subtree and requests a native edit (MB-USD-011).
2. `meshbuilder-io` converts the composed prims at the current time into native objects (meshes,
   curves, skeletons, materials), recording a native edit record with the prim path, the source
   layer and the source identifiers of every created object.
3. The prims are hidden in the stage display while native objects are shown in their place.
4. On write-back, the evaluated native result is written to the edit target: as overrides
   (`over` prims with changed attributes only) when the topology is unchanged, or as a full
   replacement spec in a new layer when topology changed. Editable history stays in the `.meshb`.
5. Cancelling discards the native objects and unhides the prims.

## Import of USD files as native content

When the user imports rather than hosts a USD file:

| USD construct | Native result |
|---|---|
| Sublayers | Flattened into the composed result; the layer list is recorded in the loss report |
| References and payloads | Each referenced asset becomes a collection; repeated references become collection instances |
| Variant sets | The selected variant is imported; other variants are imported as material or object variants where they differ only in material bindings or visibility, otherwise recorded as preserved foreign data |
| Instanceable prims and point instancers | Collection instances and instanced points |
| `UsdSkel` | Skeleton, skin binding and blend shapes (SKIN, RIG) |
| Time samples | Animation curves with linear interpolation, or a streamed cache by option |
| Unknown schemas | Preserved foreign data attached to the nearest imported object, re-emitted on export |

## Export of native scenes to USD

| Native construct | USD result |
|---|---|
| Evaluated geometry | `Mesh`, curves, points, volumes at their evaluated state; subdivision written as scheme plus control mesh |
| Instances | Instanceable prims (copies of shared data) or point instancers (scatter results) |
| Material variants, object variants | Variant sets |
| Collections marked as components | Separate component layers referenced from the main layer (MB-USD-020) |
| Animation | Time samples over the frame range; skeletal animation as `UsdSkel` animation |
| Modifier stacks, graphs, rigs | Not expressible; exported as evaluated results. Preservation of editable history is [OQ-09](../product/open-questions.md) |

## MaterialX and OpenPBR interchange

- Materials are stored as node graphs that use MaterialX standard nodes plus Arc nodes with declared
  MaterialX expansions; the surface terminal is OpenPBR.
- Export writes a MaterialX document (standalone `.mtlx` or embedded in USD) with OpenPBR surface
  nodes, texture references with color spaces, and UDIM tokens. Arc nodes are expanded to standard
  nodes; nodes without an expansion are baked to textures by option (MB-LOOK-015) and reported.
- Import reads MaterialX documents and USD MaterialX networks. Other surface models are converted to
  OpenPBR with documented parameter mappings (glTF metallic-roughness, `UsdPreviewSurface`,
  common legacy models), and every approximation is listed in the conversion report (MB-LOOK-026).
- Color spaces on textures and color inputs are carried as MaterialX color-space attributes and
  resolved through the active OpenColorIO configuration.

## Arc Scene contract for rendering

MeshBuilder never links a path tracer. Rendering goes through the Arc Scene contract (`arc-scene`),
which is aligned with USD, MaterialX and OpenPBR:

1. **Snapshot.** At session start, `meshbuilder-io` translates the evaluated scene at the current
   tick into an Arc Scene: instances with transforms, subdivision control meshes with scheme and
   level, curves, points, volumes (by reference to OpenVDB data or inline grids), OpenPBR
   materials, lights, cameras, environment, render settings, passes and the working color space.
2. **Deltas.** After each transaction, the evaluation graph reports changed outputs; only changed
   instances, transforms, materials, lights and camera parameters are sent as Arc Scene deltas.
3. **Frames.** RenderBuilder returns progressive frames and pass images over local IPC for the render
   view and frame buffer.
4. **Batch.** For render-to-file and farm jobs, a complete Arc Scene per frame range is written
   to disk (or USD with render settings, MB-RENDER-021) and handed to RenderBuilder's CLI or Hub's
   render service.
5. **Isolation.** The renderer runs as a separate process; if it exits, the session ends and the
   document is untouched.
6. **Other renderers.** A Hydra render delegate selected for a viewport or for final frames receives
   the evaluated scene as a USD stage with incremental updates in its own host process
   (MB-RENDER-022, [ADR-0007](../adr/0007-third-party-renderers-as-out-of-process-delegates.md)).

Other Arc products publish Arc Scenes the same way; MeshBuilder consumes them as Arc Scene links
(MB-ASSET-011) keyed by source element identifiers.
