# Scene and data model

How a MeshBuilder document organizes content: objects and their data, transforms and hierarchies,
the three duplication modes, layers, collections, selection sets, scene states, the document
lifecycle, and the evaluation services every other area relies on.

## Domain objects

- **Scene object**: a node in the scene hierarchy with a transform, a pivot, visibility and render
  flags, and an optional reference to object data.
- **Object data**: mesh, subdivision mesh, curve set, NURBS surface, points, volume, light, camera,
  empty, or reference proxy. Data can be shared by several scene objects.
- **Layer**: display and render state container; every scene object belongs to exactly one layer.
- **Collection**: named many-to-many grouping used for instancing, render setups and export sets.
- **Selection set**: named, stored selection of objects or components.
- **Scene state**: named snapshot of chosen scene properties for recall.

## Objects and transforms

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-001 | Typed scene objects | Creates scene objects of the types listed under domain objects; each has a stable identifier, a unique name within its parent and a type-specific data reference. Renaming never changes the identifier, so references, selections and scripts stay bound. | T1 | 4 |
| MB-SCENE-002 | Transform channels | Each object stores translation, rotation (Euler with selectable order, or quaternion), scale and shear in f64 relative to its parent. The world matrix is derived by the evaluation graph and updates whenever a parent or channel changes. | T1 | 4 |
| MB-SCENE-003 | Pivot and object offset | Stores a pivot frame separate from the geometry, editable without moving geometry (pivot-only mode) and without moving the pivot (geometry-only mode). Provides center-to-geometry, align-to-world, align-to-parent and reset commands. | T1 | 4 |
| MB-SCENE-004 | Freeze and reset transforms | Bakes current transform channels into object data so channels read identity, or resets channels to identity without touching data. Both are undoable commands and report shared data that would be affected before applying. | T1 | 4 |
| MB-SCENE-005 | Empties and helpers | Provides empties with display shapes (axes, cube, sphere, arrow, image), measuring helpers (distance, angle) and a reference grid object. Helpers never render and export as transforms only. | T1 | 4 |
| MB-SCENE-006 | Points objects | Stores point clouds with per-point attributes (position, color, radius, orientation, opacity, spherical-harmonic coefficients), displays them in the viewport and accepts point-based nodes and modifiers. Imported Gaussian-splat data is a points object with splat attributes. | T3 | 5 |
| MB-SCENE-007 | Volume objects | Stores sparse voxel grids (density, temperature, velocity and named float or vector grids) with a voxel size and transform; displays as slices, points or ray-marched preview. Volume objects are inputs and outputs of node graphs and simulations. | T2 | 5 |
| MB-SCENE-008 | User properties | Attaches typed custom properties (bool, int, float, vector, color, string, enum, object reference) with ranges and defaults to any object. Properties are animatable when numeric, visible in the property grid, and exported where the format supports custom attributes. | T1 | 4 |

## Hierarchy

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-009 | Parenting | Parents and unparents objects with a choice to keep world transform or keep local channels; rejects any edit that would create a cycle. Inheritance of translation, rotation and scale can be switched per child. | T1 | 4 |
| MB-SCENE-010 | Grouping | Groups selected objects under a new empty placed at their bounds center and ungroups by removing the empty while keeping world transforms. Groups nest without limit other than the document's hierarchy depth bound. | T1 | 4 |
| MB-SCENE-011 | Outliner | Shows the hierarchy as a virtualized tree with type icons, visibility, selectability, render and layer columns; supports search by name pattern, type filter, drag-to-reparent, multi-edit of flags and saved filter presets. Stays responsive with one million objects. | T1 | 4 |
| MB-SCENE-012 | Hierarchy selection helpers | Selects parent, children, full subtree, siblings and hierarchy roots; walks selection up, down and sideways with arrow-key commands. | T1 | 4 |
| MB-SCENE-013 | Dependency view | Displays the evaluation graph around the selection: which objects, modifiers, graphs, materials and constraints feed or consume it, with navigation to the producing node. Read-only; edits happen in the owning editor. | T2 | 4 |

## Duplication and instancing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-014 | Copy | Duplicates objects with independent data, modifiers and materials references; options select whether children, animation and input graph connections are duplicated. | T1 | 4 |
| MB-SCENE-015 | Instance | Duplicates objects that share one object data and one modifier stack; an edit to either propagates to all. Instances render and export as instances where the format supports it. | T1 | 4 |
| MB-SCENE-016 | Reference duplicate | Duplicates objects that share base data while each keeps its own modifiers above the shared point; base edits flow to all, local modifiers stay local. | T2 | 4 |
| MB-SCENE-017 | Make unique | Converts an instance or reference duplicate into an independent copy, reporting how many users the shared data had. Also available as "make all unique" for a selection. | T1 | 4 |
| MB-SCENE-018 | Duplicate with offset | Repeats a duplication N times with an incremental transform (translate, rotate, scale) about the pivot or a chosen frame, in copy, instance or reference mode. | T1 | 4 |
| MB-SCENE-019 | Collection instancing | Instances a whole collection as a single object with its own transform and an offset origin; edits to the collection appear in every instance. Instanced collections nest to a bounded depth. | T2 | 4 |
| MB-SCENE-020 | Mirror duplicate | Duplicates selected objects mirrored across a chosen plane of a chosen reference frame, in any duplication mode, without producing negative-scale transforms unless requested. | T1 | 4 |

## Layers, collections and sets

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-021 | Layers | Creates hierarchical layers with visibility, selectability (frozen), render, display mode (normal, bounding box, wire, reference) and color. Objects can follow layer display settings or override them per property. | T1 | 4 |
| MB-SCENE-022 | Current layer | New objects are placed on the current layer; commands move selection to a layer and select all objects of a layer. | T1 | 4 |
| MB-SCENE-023 | Collections | Creates collections with explicit members and rule-based members (name pattern, type, layer, user property, material). Rule results update automatically when the scene changes; an object may be in any number of collections. | T2 | 4 |
| MB-SCENE-024 | Selection sets | Stores named object selections and named component selections; component sets bind to stable component identifiers so they survive upstream topology edits wherever the components still exist. | T1 | 4 |
| MB-SCENE-025 | Hide, freeze and isolate | Hides selected or unselected objects, freezes objects against selection, and isolates the selection in one viewport without changing document visibility. All are commands and undoable. | T1 | 4 |
| MB-SCENE-026 | Scene states | Saves named combinations of chosen properties (visibility, layer state, materials, light parameters, active camera, environment) and restores them on demand. Restoring is one transaction. | T2 | 4 |
| MB-SCENE-027 | Select by property | Selects objects by name, type, layer, material, color, collection, user property value or similarity to the active object. | T1 | 4 |

## Document lifecycle

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-028 | Native document | Saves and opens `.meshb` documents (Arc document package) with explicit save, atomic writes, thumbnails and partial loading of large geometry. Documents written by any released version open in every later version. | T1 | 4 |
| MB-SCENE-029 | Undo and history | Every change is a command in a transaction recorded in the operation journal; undo and redo are unbounded except by a configurable memory budget. View changes have a separate undo stack. | T1 | 4 |
| MB-SCENE-030 | Recovery | A crash-safe recovery journal restores unsaved work after an abnormal exit, offering the recovered state next to the last saved file. | T1 | 4 |
| MB-SCENE-031 | Incremental save | Saves a numbered copy next to the document and continues editing the new file; the number of retained increments is a setting. | T2 | 4 |
| MB-SCENE-032 | Merge and import from document | Merges selected objects, collections, materials or graphs from another `.meshb`, resolving name collisions by rename, replace or skip, and reporting every decision. | T1 | 4 |
| MB-SCENE-033 | Replace by name | Replaces objects in the scene with same-named objects from another document, keeping transforms, parenting and animation of the targets when requested. | T2 | 4 |
| MB-SCENE-034 | Document compare | Compares two revisions of a document (or the working state against the saved file) and lists created, deleted and modified objects with property-level differences; selects affected objects in the viewport. | T3 | 5 |
| MB-SCENE-035 | Scene statistics | Reports object, vertex, face, triangle and instance counts for the scene, the selection and the evaluated result, plus memory per object and per cache. | T1 | 4 |

## Evaluation services

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCENE-036 | Parallel evaluation | Evaluates transforms, modifier stacks, geometry graphs, deformers and constraints as one dependency graph scheduled across all cores. Independent objects evaluate concurrently; results carry the revision they were computed from and stale results are discarded. | T1 | 4 |
| MB-SCENE-037 | Background evaluation | Long evaluations run off the interaction thread and are cancellable; the viewport keeps showing the last valid result with a progress indicator until the new one is ready. | T1 | 4 |
| MB-SCENE-038 | Frame cache | Caches evaluated geometry per frame within a memory budget, fills it in the background during idle time and playback, and invalidates only frames and objects affected by an edit. Playback from a filled cache runs at the scene frame rate. | T2 | 4 |
| MB-SCENE-039 | Evaluation profiler | Records per-node evaluation time, thread occupancy, cache hits and serialization points for a frame or frame range, and shows them as a timeline and a ranked list with links to the nodes. | T2 | 4 |
| MB-SCENE-040 | Deterministic evaluation | The same document, inputs and frame produce bit-identical evaluated geometry regardless of thread count or machine; random operators take explicit seeds. Headless evaluation can be checked against stored hashes in CI. | T2 | 4 |
