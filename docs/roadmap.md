# Roadmap

MeshBuilder milestones mapped onto the suite phases, with scope as capability identifiers,
dependencies and exit criteria stated as acceptance scenarios. There are no dates; a milestone is
complete when its exit criteria pass.

MeshBuilder starts in suite phase 4 (visualization and content), after `arc-core` L0–L6 exist and
RenderBuilder's path tracer can consume an Arc Scene. Sculpting, effects, motion capture and
collaboration depth, exchange with other applications, third-party renderers, sequencing and
compositing follow in phase 5.

| Milestone | Suite phase | Theme |
|---|---|---|
| M1 | 4 | Scene, viewport and polygon core |
| M2 | 4 | Non-destructive modeling: modifiers, subdivision, graphs, curves |
| M3 | 4 | UV, attributes, materials, lights, rendering and interchange |
| M4 | 4 | Rigging and animation |
| M5 | 4 | Scene assembly, references and pipeline |
| M6 | 5 | Sculpting, texture painting and baking |
| M7 | 5 | Motion capture, dynamics, effects and motion graphics |
| M8 | 5 | Depth and scale: collaboration, plug-ins, advanced modeling |
| M9 | 5 | Connectors, other renderers, sequencing and compositing |

## M1. Scene, viewport and polygon core

**Goal.** Open, edit, save and undo a document with polygon models in a fast, parallel viewport.

**Scope.**
- Scene: MB-SCENE-001 to MB-SCENE-005, MB-SCENE-008 to MB-SCENE-015, MB-SCENE-017, MB-SCENE-018,
  MB-SCENE-020 to MB-SCENE-022, MB-SCENE-024, MB-SCENE-025, MB-SCENE-027 to MB-SCENE-030,
  MB-SCENE-032, MB-SCENE-035 to MB-SCENE-037
- Viewport: MB-VIEW-001 to MB-VIEW-007, MB-VIEW-010 to MB-VIEW-014, MB-VIEW-016, MB-VIEW-019 to
  MB-VIEW-024, MB-VIEW-026, MB-VIEW-028, MB-VIEW-029, MB-VIEW-031, MB-VIEW-033 to MB-VIEW-037,
  MB-VIEW-039, MB-VIEW-040, MB-VIEW-042
- Polygon modeling: all T1 rows of MB-POLY-001 to MB-POLY-046
- Settings and shell: MB-PROJ-008, MB-PROJ-010, MB-PROJ-013 to MB-PROJ-016, MB-CUSTOM-001,
  MB-CUSTOM-005, MB-CUSTOM-012 to MB-CUSTOM-014, MB-CUSTOM-017 to MB-CUSTOM-019
- Automation: MB-SCRIPT-001 to MB-SCRIPT-003, MB-SCRIPT-005, MB-SCRIPT-010, MB-SCRIPT-012
- Interchange: MB-IO-001 to MB-IO-003, MB-IO-007, MB-IO-011, MB-IO-014

**Dependencies.** `arc-core`: `arc-base`, `arc-mesh` (half-edge mesh, attributes), `arc-doc`
(transactions, journal, dependency graph, package), `arc-command` (registry, prompt machines,
automation protocol), `arc-nodes` (evaluation engine), `arc-gpu`, `arc-ui`, `arc-app`,
`arc-io-mesh`. Other products: none.

**Exit criteria.**
- Steps 1 to 4 of [W01](product/workflows.md#w01-hard-surface-prop-to-game-ready-export) complete
  with primitives and direct polygon operations; the result exports as OBJ and STL and re-imports
  with identical topology counts.
- A document with 100,000 objects opens, navigates at the viewport target and saves atomically;
  killing the process mid-edit and restarting offers the recovered state.
- Every action performed in the step list is reproducible from the command log through the Python
  client against a headless session.

## M2. Non-destructive modeling

**Goal.** Modifier stacks, subdivision, geometry graphs and curves on one evaluation graph with stable
component identity.

**Scope.**
- Modifiers: MB-MOD-001 to MB-MOD-039
- Subdivision: MB-SUBD-001 to MB-SUBD-005, MB-SUBD-007 to MB-SUBD-016
- Graphs: MB-GRAPH-001 to MB-GRAPH-016, MB-GRAPH-018 to MB-GRAPH-021, MB-GRAPH-023 to MB-GRAPH-025,
  MB-GRAPH-028 to MB-GRAPH-030, MB-GRAPH-032
- Curves: MB-CURVE-001 to MB-CURVE-026, MB-CURVE-030
- Booleans and clean-up: MB-TOPO-001 to MB-TOPO-008, MB-TOPO-011, MB-TOPO-012, MB-TOPO-014,
  MB-TOPO-015, MB-TOPO-017 to MB-TOPO-020, MB-TOPO-022 to MB-TOPO-027
- Evaluation: MB-SCENE-013, MB-SCENE-016, MB-SCENE-019, MB-SCENE-023, MB-SCENE-038 to MB-SCENE-040

**Dependencies.** `arc-core`: `arc-nodes` (zones, assets, serialization), `arc-mesh` (subdivision,
booleans, simplification), `arc-geom` (curves, NURBS, planar regions), `arc-solve` (expressions),
`arc-gpu` (GPU subdivision display). Other products: none.

**Exit criteria.**
- [W01](product/workflows.md#w01-hard-surface-prop-to-game-ready-export) steps 1 to 8 and
  [W03](product/workflows.md#w03-procedural-environment-kit) pass their acceptance bullets that do
  not depend on M3 capabilities.
- A recorded edit above a bevel modifier survives changing the bevel segment count for every
  component that still exists, and reports the ones that do not.
- Reference documents evaluate to identical geometry hashes on Windows, macOS and Linux at 1, 4 and
  16 threads.

## M3. UV, attributes, materials, lights, rendering and interchange

**Goal.** Finish assets to render-ready and game-ready quality and render them through RenderBuilder.

**Scope.**
- UV: MB-UV-001 to MB-UV-026
- Attributes: MB-ATTR-001 to MB-ATTR-020
- Look development: MB-LOOK-001 to MB-LOOK-003, MB-LOOK-005 to MB-LOOK-007, MB-LOOK-010 to
  MB-LOOK-026
- Lights and cameras: MB-LIGHT-001 to MB-LIGHT-005, MB-LIGHT-007 to MB-LIGHT-014, MB-LIGHT-016 to
  MB-LIGHT-021, MB-LIGHT-023, MB-LIGHT-024
- Rendering: MB-RENDER-001 to MB-RENDER-010, MB-RENDER-012 to MB-RENDER-021
- Color: MB-PROJ-001 to MB-PROJ-007, MB-PROJ-009, MB-PROJ-011
- Interchange: MB-IO-004 to MB-IO-006, MB-IO-008, MB-IO-009, MB-IO-012, MB-IO-015, MB-IO-018,
  MB-IO-023 to MB-IO-025
- Retopology and reduction: MB-TOPO-016

**Dependencies.** `arc-core`: `arc-scene` (Arc Scene, OpenPBR), `arc-io-usd`, `arc-io-step`,
`arc-gpu` (PBR viewport). Other products: RenderBuilder interactive session and CLI (suite phase 4).

**Exit criteria.**
- [W01](product/workflows.md#w01-hard-surface-prop-to-game-ready-export),
  [W02](product/workflows.md#w02-character-base-mesh-retopology-and-uvs) (without the sculpting
  hand-off) and [W07](product/workflows.md#w07-look-development-and-turntable) pass.
- Exported glTF, USD and MaterialX files pass their independent validators in CI.

## M4. Rigging and animation

**Goal.** Rig, skin and animate characters with cached playback.

**Scope.** [20-rigging](product/capabilities/20-rigging.md), [21-skinning-and-deformation](product/capabilities/21-skinning-and-deformation.md),
[22-keyframe-animation](product/capabilities/22-keyframe-animation.md) and
[23-nonlinear-animation-and-layers](product/capabilities/23-nonlinear-animation-and-layers.md)
rows planned for phase 4; MB-IO-010, MB-IO-019 (FBX through an open reader, and point caches as available).

**Dependencies.** M2 evaluation graph and frame cache. `arc-core`: `arc-nodes` (rig nodes),
`arc-solve`. Other products: none.

**Exit criteria.**
- [W08](product/workflows.md#w08-character-rig-and-walk-cycle) passes.
- A reference character rig plays from the filled frame cache at 24 frames per second, and editing
  one key invalidates only the affected frame range.

## M5. Scene assembly, references and pipeline

**Goal.** Assemble shots and environments from referenced assets and USD stages, and integrate with
other Arc products and studio pipelines.

**Scope.**
- USD: MB-USD-001 to MB-USD-012, MB-USD-014 to MB-USD-017, MB-USD-019 to MB-USD-022, MB-USD-024
- References and assets: MB-ASSET-001 to MB-ASSET-009, MB-ASSET-011 to MB-ASSET-013, MB-ASSET-015
  to MB-ASSET-021
- Scene: MB-SCENE-026, MB-SCENE-031, MB-SCENE-033; MB-PROJ-012, MB-PROJ-017
- Scripting and customization: MB-SCRIPT-004, MB-SCRIPT-006 to MB-SCRIPT-009, MB-SCRIPT-011,
  MB-CUSTOM-002 to MB-CUSTOM-004, MB-CUSTOM-006 to MB-CUSTOM-011, MB-CUSTOM-015, MB-CUSTOM-016
- Viewport scale: MB-VIEW-008, MB-VIEW-009, MB-VIEW-015, MB-VIEW-017, MB-VIEW-025, MB-VIEW-027,
  MB-VIEW-030, MB-VIEW-032, MB-VIEW-038, MB-VIEW-041, MB-VIEW-043, MB-VIEW-044

**Dependencies.** `arc-core`: `arc-io-usd` (stage hosting and layer editing), `arc-doc` (external
references). Other products: ArchBuilder and SolidBuilder Arc Scene publishing.

**Exit criteria.**
- [W04](product/workflows.md#w04-product-visualization-from-a-solidbuilder-model),
  [W05](product/workflows.md#w05-architectural-visualization-from-an-archbuilder-model),
  [W06](product/workflows.md#w06-usd-shot-assembly-with-variants-and-layer-overrides),
  [W13](product/workflows.md#w13-headless-conversion-and-validation-in-ci) (except the Hub job step)
  and [W14](product/workflows.md#w14-studio-tool-and-configuration-roll-out) (except the plug-in)
  pass.

## M6. Sculpting, texture painting and baking

**Goal.** Sculpt, paint and bake in the same document as modeling.

**Scope.** [25-sculpting](product/capabilities/25-sculpting.md) and
[26-texture-painting-and-baking](product/capabilities/26-texture-painting-and-baking.md); MB-TOPO-013,
MB-TOPO-021; MB-LOOK-004, MB-LOOK-008, MB-LOOK-009; MB-SUBD-006.

**Dependencies.** Chunked asset storage ([ADR-0006](adr/0006-dense-geometry-as-chunked-assets.md)).
`arc-core`: `arc-doc` asset streaming, `arc-gpu` high-density display. Other products: RenderBuilder
baking support where baking uses ray tracing.

**Exit criteria.**
- [W02](product/workflows.md#w02-character-base-mesh-retopology-and-uvs) including the sculpting and
  baking hand-off, and [W12](product/workflows.md#w12-scan-clean-up-and-level-of-detail-chain), pass.

## M7. Motion capture, dynamics, effects and motion graphics

**Goal.** Clean and retarget motion capture, simulate and build procedural effects.

**Scope.** [24-motion-capture-and-retargeting](product/capabilities/24-motion-capture-and-retargeting.md),
[27-rigid-cloth-and-hair-dynamics](product/capabilities/27-rigid-cloth-and-hair-dynamics.md),
[28-particles-fluids-and-volumes](product/capabilities/28-particles-fluids-and-volumes.md),
[29-motion-graphics-and-crowds](product/capabilities/29-motion-graphics-and-crowds.md);
MB-GRAPH-017, MB-GRAPH-022, MB-GRAPH-026, MB-GRAPH-027; MB-SCENE-006, MB-SCENE-007; MB-CURVE-031,
MB-CURVE-032; MB-TOPO-009, MB-TOPO-010; MB-IO-013, MB-IO-016, MB-IO-017, MB-IO-019 to MB-IO-022;
MB-LIGHT-006, MB-LIGHT-015.

**Dependencies.** M4 animation and M2 simulation zones. `arc-core`: `arc-io-pointcloud`,
`arc-io-dxf`, `arc-io-dwg`, `arc-io-ifc`. A `meshbuilder-sim` crate is created here.

**Exit criteria.**
- [W09](product/workflows.md#w09-motion-capture-clean-up-and-retarget),
  [W10](product/workflows.md#w10-effects-shot-with-simulation-and-caches) and
  [W11](product/workflows.md#w11-motion-graphics-title-sequence) pass.

## M8. Depth and scale

**Goal.** Collaboration, sandboxed plug-ins and remaining advanced capabilities.

**Scope.** MB-SCENE-034; MB-ASSET-010, MB-ASSET-014, MB-ASSET-022 to MB-ASSET-024; MB-SCRIPT-013 to
MB-SCRIPT-018; MB-MOD-040; MB-GRAPH-031; MB-USD-013, MB-USD-018, MB-USD-023; MB-RENDER-011;
MB-VIEW-018; MB-CURVE-027 to MB-CURVE-029; MB-LIGHT-022, MB-LIGHT-025, MB-LIGHT-026.

**Dependencies.** `arc-core`: `arc-hub-api` (versioning, production tracking, change sets, render
service), WebAssembly plug-in host in `arc-app`. Other products: Arc Hub documents, production
tracking and multi-user editing (suite phase 5).

**Exit criteria.**
- [W13](product/workflows.md#w13-headless-conversion-and-validation-in-ci) including the Hub job and
  [W14](product/workflows.md#w14-studio-tool-and-configuration-roll-out) including the plug-in pass.
- Two users edit different objects of one document through Hub; a conflicting edit to the same
  object keeps both versions for comparison.

## M9. Connectors, other renderers, sequencing and compositing

**Goal.** Exchange scenes with other content-creation applications, render through other renderers
and finish shots without leaving the document.

**Scope.**
- Connectors: [30-application-connectors](product/capabilities/30-application-connectors.md)
  (MB-CONNECT-001 to MB-CONNECT-022); MB-IO-026; MB-LOOK-027
- Other renderers: MB-RENDER-022 to MB-RENDER-030
- Finishing: [31-sequencing-and-compositing](product/capabilities/31-sequencing-and-compositing.md)
  (MB-COMP-001 to MB-COMP-024, MB-DRAW2D-001 to MB-DRAW2D-012)

**Dependencies.** M4 animation, M3 rendering and interchange, M5 pipeline. `arc-core`: the Arc
exchange package reader and writer
([specification](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md)),
`arc-nodes` for compositing graphs. Other products: the `arc-connectors` repository (suite
[ADR-0017](https://github.com/ACKaraca/arc-suite/blob/main/docs/adr/0017-exchange-connectors-and-licensed-bridges.md)),
RenderBuilder for pass output.

**Exit criteria.**
- [W15](product/workflows.md#w15-round-trip-with-another-content-creation-application) and
  [W16](product/workflows.md#w16-finish-a-shot-sequence-and-composite) pass.
- A delegate crash injected during an interactive session ends only that session.
- Exchange packages from each supported host round-trip with identical identifier maps.
