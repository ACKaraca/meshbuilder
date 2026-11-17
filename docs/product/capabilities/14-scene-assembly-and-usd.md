# Scene assembly and OpenUSD

Building shots and environments from many assets with OpenUSD: stages hosted in a document, layer
stacks and edit targets, references and payloads, variants, editing USD content natively, and
publishing assets as USD components. The mapping rules are in
[scene-and-usd.md](../../architecture/scene-and-usd.md).

## Domain objects

- **Stage object**: a scene object that hosts a USD stage (root layer plus session overrides).
- **Layer stack**: the ordered sublayers of a stage with mute, lock and edit-target state.
- **Prim view**: the outliner representation of stage prims inside a stage object.
- **Native edit**: a USD prim converted to native MeshBuilder objects for editing and written back.

## Stages

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-USD-001 | Stage objects | Loads a USD file into the scene as a stage object displayed in the viewport and listed as prims in the outliner, without converting it to native objects. | T1 | 4 |
| MB-USD-002 | Payload loading | Loads and unloads payloads per prim or subtree, and opens stages with all payloads unloaded for fast inspection. | T1 | 4 |
| MB-USD-003 | Population masks | Restricts a stage to selected prim paths so large stages open only the parts needed. | T2 | 4 |
| MB-USD-004 | Prim inspection | Shows prim type, kind, purpose, attributes (with time samples), relationships, composition arcs and the layer providing each opinion. | T1 | 4 |
| MB-USD-005 | Purpose and visibility | Switches displayed purposes (render, proxy, guide) and toggles prim visibility as an opinion in the edit target. | T1 | 4 |
| MB-USD-006 | Stage time | Maps stage time codes to scene time with offset and scale, so animated stages play in sync. | T1 | 4 |
| MB-USD-007 | Stage reload | Reloads changed layers from disk while keeping session state, and reports layers that changed. | T1 | 4 |

## Layers and editing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-USD-008 | Layer stack editor | Shows sublayers in strength order; adds, creates, removes, reorders, mutes and locks layers, and marks layers with unsaved edits. | T1 | 4 |
| MB-USD-009 | Edit target | Selects the layer that receives edits; transform, visibility, material binding, variant selection and attribute edits on prims are written as opinions there. | T1 | 4 |
| MB-USD-010 | Save layers | Saves edited layers individually or all at once, in text or binary crate encoding, with a prompt-free automation equivalent. | T1 | 4 |
| MB-USD-011 | Native edit | Converts a prim subtree into native objects for full modeling, rigging or animation editing, and writes the result back to the edit target as an override or as a new layer. | T2 | 4 |
| MB-USD-012 | Merge native into stage | Writes selected native objects into a stage at a chosen prim path as new prims in the edit target. | T2 | 4 |
| MB-USD-013 | Opinion strength view | For a selected attribute, lists every layer that holds an opinion, which one wins, and lets the user jump to or remove an opinion. | T2 | 5 |

## Composition

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-USD-014 | References and payloads | Adds references and payloads to prims with target prim path and layer offset, and replaces or removes them. | T1 | 4 |
| MB-USD-015 | Variant sets | Creates variant sets and variants on prims, authors content inside a chosen variant, and switches selections in the viewport. | T1 | 4 |
| MB-USD-016 | Variant manager | Lists all variant sets in a stage with current selections, changes selections in bulk and previews combinations. | T2 | 4 |
| MB-USD-017 | Instancing | Marks prims instanceable and displays native USD instances and point instancers efficiently; edits to an instance prompt to edit the prototype or de-instance. | T1 | 4 |
| MB-USD-018 | Inherits and specializes | Displays and authors inherit and specialize arcs for class-based overrides. | T3 | 5 |
| MB-USD-019 | Material bindings | Binds MaterialX-based USD materials to prims, including collection-based bindings and binding strength. | T1 | 4 |

## Assets and validation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-USD-020 | Component publishing | Publishes selected native objects as a USD component asset: model hierarchy and kinds, geometry, materials, payload and variant structure, thumbnails and a package option (USDZ), following a configurable naming and structure rule set. | T2 | 4 |
| MB-USD-021 | Asset path diagnostics | Lists every asset path in a stage, resolves each through the configured resolver rules, flags missing or ambiguous files, and repaths in bulk. | T1 | 4 |
| MB-USD-022 | Stage validation | Runs validation rules (schema conformance, kinds and model hierarchy, unresolved paths, material binding errors, metersPerUnit and upAxis consistency) and reports findings with prim links. | T2 | 4 |
| MB-USD-023 | Shot layering templates | Creates a shot stage from a template that sets up department layers (layout, animation, effects, lighting) with the edit target on the user's department. | T2 | 5 |
| MB-USD-024 | Large-stage performance | Displays stages with millions of prims and instanced content by streaming payloads and drawing instances on the GPU, keeping navigation at the viewport frame-rate target. | T2 | 4 |
