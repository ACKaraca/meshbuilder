# Capability specification

The capability files define what MeshBuilder does, one row per observable behavior. They are the
reference for issues, pull requests, tests and the [roadmap](../../roadmap.md).

## Legend

**Identifier.** `MB-<AREA>-<NNN>`, for example `MB-POLY-021`. `MB` is the MeshBuilder prefix, `AREA`
is the token of the area file, `NNN` is numbered from 001 within the area. Identifiers are permanent:
a withdrawn capability keeps its number and is marked withdrawn, never reused.

**Tier.**

| Tier | Meaning |
|---|---|
| T1 Core | Required for first production use |
| T2 Professional | Required for parity with established professional tools |
| T3 Advanced | Specialized or differentiating |

**Phase.** The suite phase in which the capability is planned, as defined in the
suite roadmap in [`arc-suite`](https://github.com/ACKaraca/arc-suite). MeshBuilder rows
are planned in phase 4 (visualization and content) or phase 5 (depth and scale).

**Columns.** `ID | Capability | Behavior | Tier | Phase`. The behavior cell states inputs, result,
what updates automatically and limits, precisely enough to implement and test.

## Areas

| File | Area token | Scope | Rows |
|---|---|---|---|
| [01-scene-and-data-model.md](01-scene-and-data-model.md) | SCENE | Objects, transforms, hierarchy, duplication modes, layers, collections, document lifecycle, evaluation services | 40 |
| [02-viewport-and-selection.md](02-viewport-and-selection.md) | VIEW | Viewports, navigation, selection, soft selection, symmetry, manipulators, snapping | 44 |
| [03-polygon-modeling.md](03-polygon-modeling.md) | POLY | Primitives and component operations on polygon meshes | 46 |
| [04-subdivision-surfaces.md](04-subdivision-surfaces.md) | SUBD | Subdivision schemes, levels, creases, boundaries | 16 |
| [05-procedural-modifiers.md](05-procedural-modifiers.md) | MOD | Modifier stack mechanics and modeling modifiers | 40 |
| [06-geometry-node-graphs.md](06-geometry-node-graphs.md) | GRAPH | Geometry node graphs, node library, control flow, graph assets | 32 |
| [07-curves-and-nurbs.md](07-curves-and-nurbs.md) | CURVE | Curves, splines, sweeps, NURBS surfaces, strand curve data | 32 |
| [08-booleans-retopology-and-cleanup.md](08-booleans-retopology-and-cleanup.md) | TOPO | Booleans, reduction, remeshing, retopology, mesh checks | 27 |
| [09-uv-editing.md](09-uv-editing.md) | UV | UV editor, projections, seams, unfolding, packing, texel density, UDIM | 26 |
| [10-vertex-data-and-attributes.md](10-vertex-data-and-attributes.md) | ATTR | Attribute system, normals, colors, attribute paint and transfer | 20 |
| [11-materials-and-look-development.md](11-materials-and-look-development.md) | LOOK | OpenPBR materials, MaterialX graphs, textures, assignment, libraries | 26 |
| [12-lights-and-cameras.md](12-lights-and-cameras.md) | LIGHT | Lights, environments, light linking, physical cameras | 26 |
| [13-rendering-integration.md](13-rendering-integration.md) | RENDER | Viewport rendering, capture, RenderBuilder integration, render setups and passes | 21 |
| [14-scene-assembly-and-usd.md](14-scene-assembly-and-usd.md) | USD | USD stages, layers, composition, variants, publishing, validation | 24 |
| [15-references-and-asset-management.md](15-references-and-asset-management.md) | ASSET | Document references, overrides, links to Arc products, libraries, paths, Hub | 24 |
| [16-pipeline-interchange.md](16-pipeline-interchange.md) | IO | Import and export formats, conversion, loss reports, engine export | 25 |
| [17-scripting-and-extensibility.md](17-scripting-and-extensibility.md) | SCRIPT | Commands, macros, automation protocol, Python, CLI, plug-ins, assistant | 18 |
| [18-customization.md](18-customization.md) | CUSTOM | Workspaces, shortcuts, menus, preferences, accessibility, localization | 19 |
| [19-color-management-and-project-settings.md](19-color-management-and-project-settings.md) | PROJ | OpenColorIO, units, time, projects, naming rules | 17 |
| [20-rigging.md](20-rigging.md) | RIG | Joints, solvers, constraints, controls, driven relationships, generated rigs | 66 |
| [21-skinning-and-deformation.md](21-skinning-and-deformation.md) | SKIN | Binding, weights, blend shapes, correctives, shape and smoothing deformers | 56 |
| [22-keyframe-animation.md](22-keyframe-animation.md) | ANIM | Keys, curves, tangents, driven keys, path animation, shot sequencing | 51 |
| [23-nonlinear-animation-and-layers.md](23-nonlinear-animation-and-layers.md) | NLA | Clips, tracks, blending, time warps, animation layers, takes | 43 |
| [24-motion-capture-and-retargeting.md](24-motion-capture-and-retargeting.md) | MOCAP | Marker data, actor solving, full-body solver, retargeting, live devices | 55 |
| [25-sculpting.md](25-sculpting.md) | SCULPT | Brushes, multiresolution, sculpt layers, dynamic topology, masking | 55 |
| [26-texture-painting-and-baking.md](26-texture-painting-and-baking.md) | PAINT | Texture sets, paint layers, projection painting, map baking | 56 |
| [27-rigid-cloth-and-hair-dynamics.md](27-rigid-cloth-and-hair-dynamics.md) | DYN | Rigid and soft bodies, cloth, grooming, hair simulation, forces | 52 |
| [28-particles-fluids-and-volumes.md](28-particles-fluids-and-volumes.md) | FX | Particles, liquids, smoke and fire, granular, oceans, volume operations | 55 |
| [29-motion-graphics-and-crowds.md](29-motion-graphics-and-crowds.md) | MOGRAPH | Procedural instancing networks, falloffs, text animation, crowds | 48 |

Files 20 to 29 are written as a separate set; their row counts are listed as they stand.

## Area boundaries

- Every polygon operation (POLY) is also a modifier (MOD) and a graph node (GRAPH); the operation
  is specified once, in POLY.
- Shape deformers used in rigs (nonlinear, lattice, wrap, cluster, smoothing) are specified in SKIN
  and appear in the modifier stack through MB-MOD-001.
- Attribute painting (MB-ATTR-014) is the shared brush; skin weights (SKIN) and sculpt masks
  (SCULPT) use it with their own options. Texture painting is PAINT.
- Simulation zones (MB-GRAPH-026) are the engine that DYN, FX and MOGRAPH build on.
- Viewport capture (MB-RENDER-005) is the engine behind shot preview output in ANIM.
- Retopology tools are specified in TOPO and used from the sculpting workspace; overlap with SCULPT
  retopology rows is tracked in [OQ-14](../open-questions.md).

## Capabilities and issues

Issues name the capability identifiers they implement in a `Scope` section and carry an
`area: <token>` label. A capability is complete when its behavior is implemented, tested against the
behavior cell and documented; status is tracked on issues and the project board, never in these
tables. A change to a behavior updates the row in the same pull request.
