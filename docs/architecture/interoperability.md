# Interoperability

Every exchange format MeshBuilder reads or writes, by direction and fidelity level, the mapping
principles, validation, loss reporting, and what is deliberately unsupported. Fidelity levels follow
the suite policy: **view** (geometry and appearance), **data** (semantics, properties,
relationships), **edit** (native, editable result).

## Formats

| Format | Read | Write | Notes | Capabilities |
|---|---|---|---|---|
| `.meshb` (Arc document package) | edit | edit | Native document | MB-SCENE-028 |
| OpenUSD (`.usd`, `.usda`, `.usdc`, `.usdz`) | edit (hosted stage, layer editing, native edit) | data | Composition preserved when hosted; native history exported as evaluated results | MB-USD-001 to MB-USD-024, MB-IO-012 |
| Arc Scene | data (linked, read-only) | data (render sessions, publishing) | Contract between Arc products and RenderBuilder | MB-ASSET-011, MB-RENDER-007 |
| MaterialX (`.mtlx`) | edit | data | OpenPBR surfaces; Arc nodes expanded on write | MB-LOOK-025 |
| glTF 2.0 (`.gltf`, `.glb`) | data | data | PBR mapped to and from OpenPBR; skins, morph targets, clips | MB-IO-009 |
| FBX | data | data | Open reader and writer in process; a user-licensed vendor library only in a separate bridge process | MB-IO-010, MB-CONNECT-019 |
| OBJ / MTL | data | data | Materials approximated | MB-IO-011 |
| Alembic (`.abc`) | data (streamed cache) | data | Geometry caches, cameras, transforms | MB-IO-018 |
| PC2, MDD | data | data | Per-vertex animation on matching topology | MB-IO-019 |
| OpenVDB (`.vdb`) | data | data | Sparse volumes | MB-IO-020 |
| STL | view | view | Binary and text | MB-IO-014 |
| PLY | view (meshes, points, splats) | view | Splat attributes on read | MB-IO-014, MB-IO-021, MB-IO-022 |
| 3MF | data | data | Colors and units | MB-IO-014 |
| SPZ | view | — | Splat encoding | MB-IO-022 |
| LAS / LAZ, E57 | view | — | Point clouds | MB-IO-021 |
| STEP, IGES | view | — | Tessellated; assembly hierarchy and colors kept | MB-IO-015 |
| DXF, DWG | view | — | 3D geometry by layer | MB-IO-016 |
| IFC | data | — | Visualization with properties | MB-IO-017 |
| Collada (`.dae`) | view | — | Legacy assets | MB-IO-013 |
| Images (PNG, JPEG, TIFF, OpenEXR, HDR, KTX2) | data | data | Color space assigned on read; Ptex and UDIM sets supported | MB-LOOK-010, MB-PROJ-006 |
| Native scene files of other content-creation applications | data; edit where the host API allows | data; edit where the host API allows | Through connectors running in the host application, via the Arc exchange package | MB-CONNECT-001 to MB-CONNECT-022 |
| Procedural material archives | data (published specification or open runtime; otherwise through a connector) | — | Mapped to MaterialX, unmapped nodes baked | MB-LOOK-027, MB-CONNECT-021 |
| Other proprietary formats with an open reader (for example 3DM) | data | — | Behind an `arc-io-*` crate | MB-IO-026 |
| IES photometric
| BVH, C3D | data | data (BVH) | Motion and marker data | [24-motion-capture-and-retargeting](../product/capabilities/24-motion-capture-and-retargeting.md) |
| OpenTimelineIO, CMX-style edit lists | data | data | Edit sequences with speed changes and transitions | MB-ANIM-043, MB-COMP-013 |
| Video (image sequences, common video codecs through platform libraries) | view (image planes), data (sequence clips) | view (captures, sequence renders) | | MB-VIEW-007, MB-RENDER-005, MB-COMP-002, MB-COMP-014 |
| PNG, SVG (UV layout) | — | view | | MB-UV-026 |
| OpenColorIO configurations | data | — | | MB-PROJ-001 |

## Mapping principles

1. **The product owns the mapping.** Format crates in `arc-core` produce neutral models; mapping into
   the domain model lives in `meshbuilder-io`.
2. **Units and axes convert at the boundary.** The document is metres and Z-up; source and target
   conventions are converted as a root transform or baked into data, by option (MB-IO-001).
3. **Identifiers are kept.** Source identifiers (prim paths, node paths, unique ids) are stored on
   created objects so reimport updates instead of duplicating (MB-IO-003).
4. **Materials map to OpenPBR** with documented parameter tables; approximations are reported.
5. **Unmodelled content is preserved.** Content MeshBuilder does not model is attached as preserved
   foreign data and written back on export to the same format where possible.
6. **Instances stay instances** on read and write where the format supports instancing.
7. **The original file is kept** with the import record for re-mapping when the mapping improves.

## Connectors and bridges

Decision: [suite ADR-0017](https://github.com/ACKaraca/arc-suite/blob/main/docs/adr/0017-exchange-connectors-and-licensed-bridges.md).

1. **Connectors run in the host.** A connector is an add-in inside the user's own licensed copy of
   the other application. It uses only that application's public API and exchanges the Arc exchange
   package ([specification](https://github.com/ACKaraca/arc-core/blob/main/docs/architecture/interchange.md)).
   MeshBuilder reads and writes the package; it never reads the host's native file.
2. **Content types carry their own mapping.** Hierarchy, meshes with modifiers baked or mapped,
   materials, rigs, skinning, animation curves and layers, cameras and lights each map onto the
   rows of their area, and the fidelity report names what the host API does not expose
   (MB-CONNECT-004).
3. **Identity survives round trips.** Host identifiers and document ids are kept in a connector link
   so a repeated exchange updates and merges (MB-CONNECT-013, MB-CONNECT-014).
4. **Live links use public APIs only.** Sessions follow the host's change notifications where it
   offers them and revision differences otherwise (MB-CONNECT-016).
5. **Open readers run in process, vendor libraries do not.** An open reader behind `arc-io-*` is
   bounded and fuzzed like any importer; a licensed vendor library runs in a separate bridge process,
   is never distributed with Arc and is never required (MB-CONNECT-019, MB-CONNECT-020).

## Validation

- Every exporter is validated in CI by an independent reader: the Khronos glTF validator, the USD
  library's checker, an Alembic reader, an OpenVDB reader, a MaterialX validator, and re-import
  comparisons for OBJ, STL, PLY and 3MF.
- Round-trip tests compare topology counts, attribute presence, transforms within tolerance and
  material parameters for each format with edit or data fidelity.
- Connector exchange is tested against recorded exchange packages per host, with round-trip
  comparisons of identifiers, transforms, topology counts, weights and curves.
- Importers are fuzzed and bound recursion, allocation and element counts (MB-IO-007).
- Fixture files come from the suite's corpus repositories with owner, license and checksum per file;
  no vendor sample files are used.

## Loss reporting

Every import and export produces a loss report (MB-IO-002) with entries of three kinds:
**approximated** (converted with a documented difference, for example a legacy shading model mapped
to OpenPBR), **preserved** (kept as foreign data, not editable), and **dropped** (not representable,
with the reason). Reports are shown after the operation, stored with the document, and emitted as
JSON by the CLI so pipelines can fail on unexpected losses.

## Deliberately unsupported

| Not supported | Reason | Exchange instead |
|---|---|---|
| Reverse engineering undocumented formats or binaries, and running protected script files | The legal line: no published specification and no license to decode | Connectors, open readers, or USD, FBX, Alembic and glTF exported from the source application |
| Proprietary sculpt and paint formats without a connector or open reader | No published specification | OBJ or USD per subdivision level, OpenEXR or TIFF displacement and texture maps |
| Procedural material archives without a published specification or open runtime, outside a connector | Closed runtime | A connector in the owning application, or MaterialX plus baked textures |
| Live connections through private protocols | Private protocols | Live link sessions through the host's public API (MB-CONNECT-016); the automation protocol for tool authors |
| Native write of STEP, IGES, DXF, DWG, IFC | Outside the mesh domain; SolidBuilder, DraftBuilder and ArchBuilder own these | Arc Scene links in the opposite direction; OBJ, STL or glTF for mesh hand-off |
| A vendor FBX library linked into the application | License policy and crash isolation | The open reader, or the separate bridge process |

FBX is read and written in process through an open reader behind `arc-io-mesh`; a vendor library is
used only through a separate, user-installed bridge process, so no core workflow depends on it
([ADR-0004](../adr/0004-fbx-through-isolated-bridge.md)).
