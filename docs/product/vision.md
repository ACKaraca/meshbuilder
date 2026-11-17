# MeshBuilder vision

Why MeshBuilder exists, who it serves, the principles that shape it, what it deliberately does not
do, and where it stands among professional content-creation tools.

## Problem and purpose

3D content for film, games, visualization, advertising and product communication is produced by
chains of separate applications: one for modeling, one for sculpting, one for character animation
and motion capture, one for effects, each with its own scene format, its own evaluation model and its
own scripting language. Every hand-off between them loses rigs, materials or history, and every
studio writes glue code to keep them aligned. Scene files are closed, binary and merge-hostile, so
version control and review are bolted on. Evaluation engines built decades ago run on one core for
much of their work, and procedural tools exist as separate islands per task.

MeshBuilder is one application in which modeling, sculpting, texturing, rigging, animation,
motion-capture editing, simulation and scene assembly share one document, one evaluation graph, one
procedural node system and one command model. It speaks OpenUSD, MaterialX and OpenPBR as its native
vocabulary, runs identically on Windows, macOS and Linux on any vendor's GPU, and works offline with
no account.

## Primary users and deliverables

| User | Deliverables |
|---|---|
| Modeler (props, environments, hard surface) | Game-ready and render-ready meshes with UVs, LOD chains, USD components |
| Character artist | Sculpted, retopologized, UV-mapped and textured characters |
| Rigger and technical animator | Rigs, skin bindings, correctives, reusable rig components |
| Animator | Keyframed and clip-based animation, cleaned motion capture, shot previews |
| Effects artist | Simulations and procedural effects baked to Alembic, OpenVDB or USD caches |
| Look-development and lighting artist | MaterialX/OpenPBR material libraries, lit shots rendered through RenderBuilder |
| Visualization artist | Rendered stills and sequences of models linked from ArchBuilder, SolidBuilder and CivilBuilder |
| Shot finisher | Edited and composited shots with audio, ready for review |
| Pipeline technical director | Scripts, plug-ins, graph assets, headless conversion and validation jobs |

## Product principles

1. **One graph.** The modifier stack, geometry graphs, rigs, deformers and simulations are nodes in
   one dependency graph; there is no second evaluation system to learn or to keep consistent.
2. **Non-destructive by default.** Operations stay editable; direct edits bind to stable component
   identifiers so they survive upstream changes.
3. **Open vocabulary.** Scene description follows OpenUSD concepts, materials are MaterialX graphs
   with OpenPBR surfaces, color follows OpenColorIO. Native data maps to these without invention.
4. **Parallel and deterministic.** Evaluation uses every core from the first release and produces
   identical results on every machine.
5. **Everything is a command.** The UI, scripts, plug-ins, the CLI, Hub jobs and the assistant drive
   the same commands; no capability is available only through the mouse.
6. **The document is the authority.** Viewport display, render scenes and exports are derived and
   can be rebuilt; caches are disposable.
7. **No code in documents.** Opening a document never runs code.

## Scope

In scope: everything specified in the [capability files](capabilities/README.md): scene and data
model, viewport and selection, polygon and subdivision modeling, procedural modifiers and geometry
graphs, curves and NURBS, booleans and retopology, UV editing, attributes, materials and look
development, lights and cameras, viewport rendering and RenderBuilder integration, OpenUSD scene
assembly, references and asset management, interchange, scripting and plug-ins, customization,
color management, rigging, skinning and deformation, keyframe and nonlinear animation, motion
capture, sculpting, texture painting and baking, dynamics, particles, fluids and volumes, motion
graphics and crowds, exchange with other content-creation applications through connectors and live
links, third-party renderers through out-of-process Hydra render delegates, a sequence editor and a
node compositor for finishing shots, and drawing-based 2D animation in the 3D scene.

## Non-goals

| Non-goal | Reason |
|---|---|
| A built-in production path tracer | Rendering is RenderBuilder's job through the Arc Scene contract; one renderer is maintained for the suite and crashes stay isolated. Other renderers run as out-of-process render delegates (MB-RENDER-022). |
| Architectural object families (walls, doors, windows, stairs, railings) | Building elements belong to ArchBuilder and arrive through Arc Scene links (MB-ASSET-011) with their semantics intact. |
| Legacy surface representations (patch grids, triangle-only editable meshes, legacy hierarchical subdivision surfaces) | Polygon meshes with subdivision and NURBS cover the same results with one code path; imports convert them. |
| Procedural material archives without a published specification or open runtime | Decoding them would need reverse engineering; they are read through a connector in the application that owns them (MB-CONNECT-021) or exchanged as MaterialX with baked textures (MB-LOOK-015). |
| Cloud-only generative services as built-in features | Conflicts with offline, account-free operation; the assistant panel uses user-configured providers only. |
| Native (dynamic library) plug-ins and embedded interpreters | Ambient authority and crash risk; extensions use the automation protocol and sandboxed WebAssembly. Renderers and bridges run as separate processes, never loaded in process. |
| Reverse engineering undocumented formats or binaries, and running protected script files of other applications | The legal line: no published specification and no license to decode. Native scene files are exchanged through connectors that use the host application's public API (MB-CONNECT-001), open readers (MB-IO-026) and USD, FBX, Alembic and glTF. |

## Positioning

MeshBuilder covers the professional scope served today by Autodesk 3ds Max, Autodesk Maya,
Autodesk MotionBuilder and Autodesk Mudbox, Blender, SideFX Houdini, Maxon Cinema 4D and
Maxon ZBrush: polygon and subdivision modeling, sculpting, look development, rigging, animation,
motion-capture editing, simulation and scene assembly.

## Differentiators

- **One procedural node system** for modeling, rigging, simulation and motion graphics, with the
  modifier stack as its linear view ([ADR-0001](../adr/0001-modifier-stack-as-graph-view.md),
  MB-MOD-008, MB-GRAPH-026).
- **Stable component identity**: direct edits, selections and overrides bind to identifiers that
  survive topology changes upstream ([ADR-0002](../adr/0002-stable-component-identity.md),
  MB-MOD-006, MB-SCENE-024).
- **OpenUSD, MaterialX and OpenPBR as native vocabulary**: stages edited in place, native edit and
  write-back, component publishing, asset-path diagnostics (MB-USD-011, MB-USD-020, MB-USD-021,
  MB-LOOK-001).
- **Parallel, deterministic evaluation from the first release**, with a profiler and a frame cache
  (MB-SCENE-036, MB-SCENE-038, MB-SCENE-039, MB-SCENE-040).
- **Modeling, sculpting, texturing, animation and motion-capture editing in one application** on one
  document, so hand-offs do not lose rigs, layers or history.
- **Vendor-neutral GPU** and identical features on Windows, macOS and Linux (MB-VIEW-010).
- **Exchange with other applications' scenes**: connectors running inside the other application
  carry hierarchy, meshes, materials, rigs, skinning and animation with identifiers that survive
  repeated round trips, and live link sessions where the public API allows (MB-CONNECT-001,
  MB-CONNECT-013, MB-CONNECT-016).
- **Any Hydra renderer**, crash isolated in its own process, selectable per viewport and for final
  frames (MB-RENDER-022, MB-RENDER-024, MB-RENDER-025).
- **Shot finishing in the same document**: sequence editor, pass-based compositor and drawing
  animation (MB-COMP-001, MB-COMP-015, MB-DRAW2D-001).
- **Live links to ArchBuilder, SolidBuilder and CivilBuilder models** for visualization with
  refinements that survive source updates (MB-ASSET-011 to MB-ASSET-014).
- **Safe documents**: no code runs on open (MB-SCRIPT-010); plug-ins are sandboxed with explicit
  capability grants (MB-SCRIPT-014, MB-SCRIPT-015).
- **Open, inspectable documents** with document compare and Hub versioning (MB-SCENE-034,
  MB-ASSET-022), and element-level multi-user editing (MB-ASSET-024).
- **Headless everything**: graph evaluation, conversion, validation and viewport capture from the CLI
  (MB-SCRIPT-012, MB-GRAPH-032).
