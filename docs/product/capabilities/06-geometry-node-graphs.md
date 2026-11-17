# Geometry node graphs

Visual programming over geometry: typed dataflow graphs that create and transform meshes, curves,
points, instances and volumes. Graphs run on the same evaluation engine as the modifier stack and are
the one procedural system that rigging, simulation and motion graphics build on.

## Domain objects

- **Geometry graph**: a typed dataflow graph owned by an object (as its stack) or stored as an asset.
- **Node**: an operation with typed input and output sockets; geometry, field, scalar, vector,
  matrix, string, object, collection, material and bundle types.
- **Field**: a lazily evaluated per-element expression (per point, edge, face, corner, instance)
  evaluated in the context of the geometry it is applied to.
- **Graph asset**: a reusable graph with a published interface, version and description.

## Graph editing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-GRAPH-001 | Graph editor | Edits graphs on a zoomable canvas: add nodes by search, connect sockets with type checking, reroute points, frames with labels, comments, alignment and auto-layout. | T1 | 4 |
| MB-GRAPH-002 | Live evaluation | Re-evaluates the graph on any edit and shows the result in the viewport; only nodes downstream of the change recompute. | T1 | 4 |
| MB-GRAPH-003 | Socket inspection | Shows the value, element count, domain sizes and attribute list on any socket on hover, and a spreadsheet of the full geometry at a pinned socket. | T1 | 4 |
| MB-GRAPH-004 | Viewer node | Displays the output of any node in the viewport in place of the final result, without rewiring. | T1 | 4 |
| MB-GRAPH-005 | Node groups | Collapses a node selection into a group node with an editable interface (inputs, outputs, defaults, ranges, tooltips) and expands it back. | T1 | 4 |
| MB-GRAPH-006 | Graph assets | Saves a graph or group as an asset with name, version, category and description; other objects and documents use it by reference and receive updates when the asset changes, with version pinning available. | T2 | 4 |
| MB-GRAPH-007 | Published parameters | Exposes chosen inputs as parameters in the property grid and modifier stack, with manipulators where the type allows. | T1 | 4 |
| MB-GRAPH-008 | Error reporting | Marks nodes with type errors, invalid inputs or runtime failures, with a message on the node and the error propagated as an empty output rather than a crash. | T1 | 4 |
| MB-GRAPH-009 | Node timing overlay | Shows evaluation time per node on the canvas and highlights the most expensive nodes. | T2 | 4 |

## Node library

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-GRAPH-010 | Geometry inputs | Reads object geometry (in object or relative space), collections, the self object, scene time and document settings. | T1 | 4 |
| MB-GRAPH-011 | Mesh primitive nodes | Generates every primitive of MB-POLY-001 and grids, lines and points with parameter sockets. | T1 | 4 |
| MB-GRAPH-012 | Mesh operation nodes | Provides every polygon and modifier operation as a node, taking selections as fields. | T1 | 4 |
| MB-GRAPH-013 | Attribute nodes | Reads, writes, captures, transfers, removes and statistics-summarizes named attributes on any domain; converts between domains with defined interpolation. | T1 | 4 |
| MB-GRAPH-014 | Math and field nodes | Scalar, vector, matrix, boolean and color math, comparison, mix, map range, clamp, curve mapping, noise and texture fields, position, normal, index and identifier fields. | T1 | 4 |
| MB-GRAPH-015 | Curve nodes | Creates, resamples, fillets, trims, offsets and sweeps curves, converts curves to meshes and points, and samples curve positions and frames. | T1 | 4 |
| MB-GRAPH-016 | Point and instance nodes | Distributes points on surfaces and in volumes, instances geometry on points, realizes instances into geometry, and sets instance transforms and attributes. | T1 | 4 |
| MB-GRAPH-017 | Volume nodes | Converts meshes and points to signed distance or density volumes, filters, combines and samples volumes, and meshes volumes back with adaptive resolution. | T2 | 5 |
| MB-GRAPH-018 | Proximity and ray nodes | Samples nearest surface, nearest point, raycasts against geometry, and computes geodesic distances. | T2 | 4 |
| MB-GRAPH-019 | Selection and grouping nodes | Builds selections from fields, groups elements by attribute value, and splits geometry by group. | T1 | 4 |
| MB-GRAPH-020 | String and text nodes | Builds strings, converts values to text and generates text curves and meshes. | T2 | 4 |
| MB-GRAPH-021 | Material nodes | Assigns materials per face by field and reads material indices. | T1 | 4 |
| MB-GRAPH-022 | Heightfield nodes | Generates and edits height fields from noise and images, applies erosion and terracing approximations, and converts to meshes with adaptive density. | T3 | 5 |

## Control flow

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-GRAPH-023 | Switch and conditional | Chooses between inputs by a boolean or index; unused branches do not evaluate. | T1 | 4 |
| MB-GRAPH-024 | Repeat zones | Repeats a subgraph a bounded number of times, passing state between iterations, with an iteration limit enforced by the engine. | T2 | 4 |
| MB-GRAPH-025 | For-each zones | Evaluates a subgraph per element, group or instance in parallel and joins the results. | T2 | 4 |
| MB-GRAPH-026 | Simulation zones | Carries state from one frame to the next, with cache and reset rules, so graphs express time-stepped behavior; solvers in 27 and 28 build on this. | T2 | 5 |
| MB-GRAPH-027 | Bundles and closures | Packs several typed values into one bundle socket and passes subgraphs as closure values that other nodes invoke, so libraries accept user-supplied behavior. | T3 | 5 |
| MB-GRAPH-028 | Expression node | Evaluates a typed, bounded expression language over fields and scalars, compiled once per edit, with no file, network or process access. | T2 | 4 |

## Integration

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-GRAPH-029 | Graph-driven objects | Any object's geometry can be produced entirely by a graph, including objects with no base data. | T1 | 4 |
| MB-GRAPH-030 | Bake graph output | Bakes the graph result at the current frame or a frame range into object data or a cache asset, with the graph kept for re-baking. | T1 | 4 |
| MB-GRAPH-031 | Plug-in nodes | Hosts nodes implemented by sandboxed plug-ins with declared socket types and deterministic evaluation. | T2 | 5 |
| MB-GRAPH-032 | Headless graph evaluation | Evaluates a graph asset from the CLI or automation protocol with given inputs and writes outputs to files, without the desktop application. | T2 | 4 |
