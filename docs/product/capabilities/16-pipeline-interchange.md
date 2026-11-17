# Pipeline interchange

Import and export of scene, geometry, cache and point formats, with axis and unit conversion, presets,
validation and loss reports. Fidelity levels per format and direction are listed in
[interoperability.md](../../architecture/interoperability.md); OpenUSD stage work is in
[14-scene-assembly-and-usd.md](14-scene-assembly-and-usd.md).

## Domain objects

- **Import session**: source file, options, mapping decisions, created objects and loss report.
- **Export preset**: named format, options, object set and post-export validation.
- **Loss report**: list of source items that were approximated, preserved as foreign data or
  dropped, each with a reason.
- **Preserved foreign data**: source content MeshBuilder does not model, kept with the import for
  round trip.

## Common behavior

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-IO-001 | Unit and axis conversion | Converts source units and up axis to the document (metres, Z-up) on import and to the target convention on export, as a transform at the root or baked into data by option, recorded in the loss report. | T1 | 4 |
| MB-IO-002 | Loss reports | Every import and export produces a loss report listing approximations, preserved foreign data and dropped items; reports are viewable, saved with the document and emitted as JSON from the CLI. | T1 | 4 |
| MB-IO-003 | Source identifiers | Stores source-format identifiers and names on created objects so re-importing an updated file updates the same objects instead of duplicating them. | T1 | 4 |
| MB-IO-004 | Reimport | Re-imports an updated source file, updating changed objects, adding new ones and reporting removed ones, while keeping local material and modifier changes that the user marked to keep. | T2 | 4 |
| MB-IO-005 | Export presets | Saves export settings and object selections (by collection or rule) as named presets runnable from the menu, CLI and automation protocol. | T1 | 4 |
| MB-IO-006 | Export validation | Validates exports against format rules with an independent reader and target checks (triangle budget, texture size, naming, scale) defined in the preset. | T2 | 4 |
| MB-IO-007 | Hostile-input bounds | Every importer bounds recursion, allocation and element counts, rejects malformed data with a message, and never leaves a partial import in the document. | T1 | 4 |
| MB-IO-008 | Batch conversion | Converts many files between supported formats with a preset from the CLI, writing a report per file. | T1 | 4 |

## Scene formats

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-IO-009 | glTF 2.0 | Imports and exports glTF and GLB: hierarchy, meshes, PBR materials mapped to OpenPBR, textures, cameras, punctual lights, skins, morph targets, animation clips, instancing and mesh compression extensions. | T1 | 4 |
| MB-IO-010 | FBX | Imports and exports FBX scenes (hierarchy, meshes with smoothing and normals, materials, cameras, lights, skins, blend shapes, animation takes) through an isolated conversion process; FBX is never required for a core workflow. | T2 | 4 |
| MB-IO-011 | OBJ | Imports and exports OBJ with MTL materials, groups, smoothing groups, multiple objects and vertex colors where present. | T1 | 4 |
| MB-IO-012 | USD export of native scenes | Exports the native scene or a selection to USD (text, binary or USDZ) with hierarchy, instancing, materials, lights, cameras, animation, skeletons and blend shapes, and variant sets from material and object variants. | T1 | 4 |
| MB-IO-013 | Collada | Imports Collada scenes for legacy assets at view fidelity. | T3 | 5 |
| MB-IO-014 | 3D printing formats | Imports and exports STL (binary and text), PLY and 3MF, with units, colors where supported and a watertight check before export. | T1 | 4 |
| MB-IO-015 | CAD import | Imports STEP and IGES by tessellation with chord and angle tolerance, keeping the assembly hierarchy, names and colors. | T2 | 4 |
| MB-IO-016 | DXF and DWG geometry | Imports DXF and DWG 3D faces, meshes, polylines and blocks as meshes, curves and instances, by layer. | T2 | 5 |
| MB-IO-017 | IFC visualization import | Imports IFC models as meshes with element hierarchy, names and property sets as user properties, for visualization. | T2 | 5 |

## Caches and points

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-IO-018 | Alembic | Imports Alembic caches as streamed animated geometry (meshes, curves, points, cameras, transforms, normals, UVs, attributes) and exports evaluated geometry over a frame range with sub-frame samples. | T1 | 4 |
| MB-IO-019 | Point-cache formats | Reads and writes per-vertex animation caches in PC2 and MDD formats onto matching meshes. | T2 | 5 |
| MB-IO-020 | OpenVDB | Imports and exports sparse volumes as OpenVDB grids. | T2 | 5 |
| MB-IO-021 | Point clouds | Imports PLY, LAS/LAZ and E57 point clouds as points objects, with decimation on import. | T2 | 5 |
| MB-IO-022 | Gaussian splats | Imports splat data in PLY and SPZ encodings as points with splat attributes for display, placement and render hand-off. | T3 | 5 |
| MB-IO-023 | Cache streaming | Cached geometry streams from disk per frame within a memory budget instead of loading the whole file. | T1 | 4 |

## Engine export

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-IO-024 | Game-ready export | Exports meshes with LOD groups, collision proxies by naming rule, sockets as empties, and animation clips per action or clip track, with triangulation and tangent options. | T2 | 4 |
| MB-IO-025 | Texture export settings | Converts and resizes textures on export (format, bit depth, channel packing, max size) according to the preset. | T2 | 4 |
