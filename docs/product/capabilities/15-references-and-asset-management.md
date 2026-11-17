# References and asset management

Using content from other files without copying it: native document references with local overrides,
namespaces, proxies and representations, locked containers, live links to other Arc products, asset
libraries, path management, packaging and collaboration through Arc Hub.

## Domain objects

- **Document reference**: link from a document to another `.meshb` (whole or a chosen collection),
  with a namespace, a transform and recorded local overrides.
- **Representation**: alternative form of a referenced asset (full, proxy mesh, bounding box,
  cache, USD).
- **Container**: a collection published from a document with a declared set of editable parameters.
- **Arc Scene link**: a live reference to an Arc Scene published by another Arc product.
- **Asset library**: a folder or Hub location of catalogued assets with metadata.

## Document references

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSET-001 | Reference documents | References another `.meshb` document or a collection in it; the referenced content appears read-only in the outliner under a reference root with its own transform. | T1 | 4 |
| MB-ASSET-002 | Local overrides | Records edits to referenced objects (transforms, visibility, materials, property values, added modifiers) as overrides in the referencing document, keyed by stable identifiers, and reapplies them when the reference reloads. | T1 | 4 |
| MB-ASSET-003 | Override management | Lists overrides per reference with their targets, removes selected overrides, and flags overrides whose targets no longer exist. | T2 | 4 |
| MB-ASSET-004 | Load, unload, reload | Loads, unloads and reloads references individually or in bulk; unloaded references keep their overrides and placement. | T1 | 4 |
| MB-ASSET-005 | Replace reference | Points a reference at another file or version while keeping overrides whose targets exist in the new file. | T1 | 4 |
| MB-ASSET-006 | Import reference | Converts a reference into local objects, baking overrides. | T1 | 4 |
| MB-ASSET-007 | Namespaces | Places referenced and merged content in a namespace prefix to avoid name clashes; renames, merges and removes namespaces. Identifiers, not names, remain the link. | T1 | 4 |
| MB-ASSET-008 | Nested references | Resolves references inside referenced documents to a bounded depth and detects cycles with a clear error. | T1 | 4 |
| MB-ASSET-009 | Representations | Switches a reference between full, proxy, bounding box, cache and USD representations per instance or in bulk, keeping placement and overrides. | T2 | 4 |
| MB-ASSET-010 | Containers | Publishes a collection as a container with chosen editable parameters; referencing documents can change only those parameters, and the source can be updated while edits are kept. | T2 | 5 |

## Links to other Arc products

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSET-011 | Arc Scene links | Links an Arc Scene exported by ArchBuilder, SolidBuilder, CivilBuilder or DraftBuilder; geometry, materials and object metadata appear as a read-only reference that updates when the source publishes a new version. | T2 | 4 |
| MB-ASSET-012 | Link refinement | Assigns local materials, hides elements, and attaches extra objects to linked elements by stable source identifiers, so refinements survive source updates. | T2 | 4 |
| MB-ASSET-013 | Link change report | On update, lists added, removed and changed source elements and the local refinements affected. | T2 | 4 |
| MB-ASSET-014 | Live link session | Follows a running Arc product's Arc Scene stream so model edits in the source appear in MeshBuilder within seconds while both applications run. | T3 | 5 |

## Asset libraries

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSET-015 | Asset browser | Browses objects, collections, materials, graph assets, poses and environments from local folders and Hub libraries as thumbnails with tags, catalogs and search; drag-and-drop inserts by copy, instance or reference. | T1 | 4 |
| MB-ASSET-016 | Mark as asset | Marks an object, collection, material or graph as an asset with name, catalog, tags, description and generated thumbnail. | T1 | 4 |
| MB-ASSET-017 | Library indexing | Indexes library folders in the background so browsing does not open documents. | T2 | 4 |

## Paths and packaging

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSET-018 | External file manager | Lists every external file (textures, caches, references, USD layers, IES profiles, audio) with resolved path, status and size; relinks missing files by folder search and repaths in bulk with find and replace. | T1 | 4 |
| MB-ASSET-019 | Relative paths | Stores paths relative to the project root or the document when possible, with path variables for studio roots. | T1 | 4 |
| MB-ASSET-020 | Package document | Collects the document and all dependencies into a folder or archive with rewritten relative paths, for hand-off or farm submission. | T1 | 4 |
| MB-ASSET-021 | Purge unused | Removes unused materials, textures, graphs, layers, empty collections and orphaned data, listing what will be removed before applying. | T1 | 4 |

## Collaboration

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ASSET-022 | Hub file versioning | Opens, saves, checks out and checks in documents on Arc Hub with versions, comments and lifecycle state, and shows whether a newer version exists. | T2 | 5 |
| MB-ASSET-023 | Production tracking context | Shows the Hub task and entity linked to the open document and publishes versions (document, renders, captures) to it for review. | T2 | 5 |
| MB-ASSET-024 | Multi-user editing | Lets several users edit one document through Hub by borrowing objects; transactions travel as change sets and conflicts keep both versions for comparison. | T3 | 5 |
