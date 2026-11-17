# Glossary

Terms used in MeshBuilder documentation, one line each.

| Term | Meaning |
|---|---|
| Arc document package | The single-file SQLite container used by all Arc products; MeshBuilder's native `.meshb` file |
| Arc Scene | Renderable scene description exchanged between Arc products and RenderBuilder, aligned with OpenUSD, MaterialX and OpenPBR |
| Arc exchange package | Open, documented container written and read by connectors to carry scene content between an Arc product and another application |
| Arc Scene link | Read-only reference to an Arc Scene published by another Arc product, refreshed on new versions |
| Asset (library) | An object, collection, material, graph or pose marked for reuse and listed in the asset browser |
| Attribute | Named, typed per-element data on a domain of a geometry |
| Attribute paint | Brush that paints colors or scalar values into attributes |
| Bake | Write the result of an evaluation (graph, simulation, texture) into stored data or a cache |
| Chunk | Fixed-size unit of dense data stored as a content-addressed asset in the package |
| Collection | Many-to-many grouping of scene objects, used for instancing, render setups and export sets |
| Collection instance | An object that displays a whole collection with its own transform |
| Component | A vertex, edge or face of a mesh |
| Component identifier | Stable 64-bit identity of a component that survives upstream changes |
| Compositing graph | Node graph of floating-point images that combines render passes and clips |
| Connector | Add-in running inside another application, using only its public API, that reads and writes the Arc exchange package |
| Connector link | Stored map between host identifiers and document ids that makes repeated exchanges update the same objects |
| Container | A published collection whose referencing documents may change only declared parameters |
| Copy, instance, reference duplicate | The three duplication modes: independent data; shared data and stack; shared base data with local modifiers |
| Crease | Sharpness value on an edge or vertex that controls subdivision smoothing |
| Document reference | Link to another `.meshb` document or collection, with local overrides |
| Drawing object | Scene object holding layers of stroke frames for drawing-based animation |
| Edit sequence | Timeline of clips, transitions and audio used to review and finish shots |
| Edit target | The USD layer that receives edits made to a hosted stage |
| Evaluation graph | The single dependency graph that computes all derived data of a document |
| Field | Per-element expression in a geometry graph, evaluated in the context of the geometry it is applied to |
| Frame cache | Memory-budgeted store of evaluated results per object and frame |
| Geometry graph | Typed dataflow graph that produces or transforms geometry |
| Graph asset | Reusable, versioned geometry graph with a published interface |
| Layer | Display and render state container; each object belongs to exactly one |
| Layer stack | The ordered sublayers of a USD stage |
| Licensed bridge | Separate process that calls a conversion library the user has installed and licensed |
| Live link session | Running connection between the document and another running application that streams changes |
| Loss report | Record of what an import or export approximated, preserved or dropped |
| MaterialX | Open standard for material node graphs, used as MeshBuilder's material graph vocabulary |
| Modifier | A geometry graph node shown as an entry in an object's modifier stack |
| Modifier stack | Linear view of an object's geometry graph from base data to result |
| Namespace | Name prefix applied to referenced or merged content to avoid name clashes |
| Native edit | Conversion of USD prims to native objects for editing, with write-back to a layer |
| OpenPBR | Open physically based surface model used as the standard material terminal |
| Override | A local change to referenced content, stored in the referencing document by identifier |
| Payload | A USD reference that can be loaded and unloaded on demand |
| Points object | Object holding a point cloud with per-point attributes, including splat data |
| Preserved foreign data | Imported content that MeshBuilder does not model, kept for round trip |
| Recorded edit | Modifier that stores direct component edits keyed by component identifiers |
| Render delegate | Hydra render delegate of another renderer, run in its own process |
| Render pass | Named output channel requested from the renderer |
| Render setup | Named configuration of included collections, overrides and passes rendered as one job |
| Representation | Alternative form of a referenced asset: full, proxy, bounding box, cache or USD |
| Repeat zone, for-each zone | Graph regions that iterate a subgraph a bounded number of times or per element |
| Scene object | Node in the scene hierarchy with a transform and optional object data |
| Scene state | Named snapshot of chosen scene properties for recall |
| Selection set | Stored named selection of objects or components |
| Simulation zone | Graph region that carries state from one frame to the next |
| Soft selection | Weighted extension of a component transform to neighbors by a falloff |
| Stage object | Scene object hosting a USD stage |
| Strand curves | Curve set of many short curves attached to a surface, for hair and fibers |
| Tick | Time unit of 1/705,600,000 second used to store time |
| UDIM tile | Unit square of UV space addressed by tile number |
| UV map | Named face-corner attribute of texture coordinates |
| Variant set | USD mechanism for switchable alternatives on a prim; native material and object variants map to it |
| Viewport capture | Recording of a camera's viewport over a frame range to images or video |
