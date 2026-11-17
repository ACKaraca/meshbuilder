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
graphics and crowds.

## Non-goals

| Non-goal | Reason |
|---|---|
| A built-in production path tracer | Rendering is RenderBuilder's job through the Arc Scene contract; one renderer is maintained for the suite and crashes stay isolated. |
| In-process third-party renderer plug-ins | They would require native plug-ins and break crash isolation; other renderers consume the exported USD stage (MB-RENDER-021). |
| Architectural object families (walls, doors, windows, stairs, railings) | Building elements belong to ArchBuilder and arrive through Arc Scene links (MB-ASSET-011) with their semantics intact. |
| Legacy surface representations (patch grids, triangle-only editable meshes, legacy hierarchical subdivision surfaces) | Polygon meshes with subdivision and NURBS cover the same results with one code path; imports convert them. |
| Video sequencing and image compositing | Separate disciplines with dedicated open tools; renders and captures export as standard image sequences and edit lists. |
| 2D drawing-based animation in the 3D scene | A distinct medium; not required by the target deliverables. Reconsidered if the user base demands it. |
| Closed procedural material archives that need a proprietary runtime | No published specification; materials exchange as MaterialX with baked textures (MB-LOOK-015). |
| Cloud-only generative services as built-in features | Conflicts with offline, account-free operation; the assistant panel uses user-configured providers only. |
| Native (dynamic library) plug-ins and embedded interpreters | Ambient authority and crash risk; extensions use the automation protocol and sandboxed WebAssembly. |
| Reading other applications' proprietary native scene files | No published specification; those scenes are exchanged through USD, FBX, Alembic and glTF. |

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
- **Live links to ArchBuilder, SolidBuilder and CivilBuilder models** for visualization with
  refinements that survive source updates (MB-ASSET-011 to MB-ASSET-014).
- **Safe documents**: no code runs on open (MB-SCRIPT-010); plug-ins are sandboxed with explicit
  capability grants (MB-SCRIPT-014, MB-SCRIPT-015).
- **Open, inspectable documents** with document compare and Hub versioning (MB-SCENE-034,
  MB-ASSET-022), and element-level multi-user editing (MB-ASSET-024).
- **Headless everything**: graph evaluation, conversion, validation and viewport capture from the CLI
  (MB-SCRIPT-012, MB-GRAPH-032).
