# Booleans, retopology and mesh clean-up

Mesh booleans, polygon reduction, remeshing, manual and automatic retopology, and the checks and
fixes that make meshes valid for subdivision, rendering, printing and export. The retopology tools
here are the ones the sculpting workspace uses; sculpt-specific hand-off is in
[25-sculpting.md](25-sculpting.md).

## Domain objects

- **Boolean operation**: target, operand list (objects or collections), operation and solver
  settings; lives in the stack as a modifier or is applied once.
- **Retopology session**: a target mesh being built on a reference surface with snapping.
- **Mesh check report**: list of problems found, each with components, severity and a fix.

## Booleans

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-TOPO-001 | Union, difference, intersection | Combines closed meshes by union, difference and intersection with exact predicates, producing watertight results for watertight inputs. | T1 | 4 |
| MB-TOPO-002 | Cut and split | Cuts the target by an operand's surface without removing material, or splits the target into inside and outside parts. | T1 | 4 |
| MB-TOPO-003 | Multi-operand booleans | Applies an ordered list of operands, each with its own operation, in one evaluation; operands can be collections. | T1 | 4 |
| MB-TOPO-004 | Open-mesh handling | Handles operands with holes or self-intersections in a tolerant mode that classifies by winding number, and reports which inputs were not closed. | T2 | 4 |
| MB-TOPO-005 | Coplanar and touching faces | Resolves coplanar and touching faces consistently without slivers, using document tolerances from `arc-base`. | T1 | 4 |
| MB-TOPO-006 | Attribute carry-over | Carries UVs, normals, materials, creases and custom attributes from both inputs to the result, interpolating across cut faces. | T1 | 4 |
| MB-TOPO-007 | Result clean-up | Optionally removes redundant edges on coplanar regions, merges co-located vertices and produces quad-dominant cut topology. | T2 | 4 |
| MB-TOPO-008 | Cutter display | Displays operand objects as wire or transparent cutters, hidden from render and export by default. | T1 | 4 |
| MB-TOPO-009 | Voxel booleans | Combines meshes through signed distance volumes for robust results on bad input, at a chosen voxel size, meshed back with adaptive density. | T2 | 5 |
| MB-TOPO-010 | Fracture | Splits a mesh into pieces by cutter objects, a cell pattern from scattered points, or planes, with interior material and seed. | T3 | 5 |

## Reduction and remeshing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-TOPO-011 | Voxel remesh | Rebuilds a mesh as an even quad-dominant surface at a voxel size, closing holes and removing internal geometry; optionally projects attributes back. | T2 | 4 |
| MB-TOPO-012 | Isotropic remesh | Remeshes to even triangles at a target edge length while preserving sharp features and boundaries. | T2 | 4 |
| MB-TOPO-013 | Automatic quad retopology | Generates a quad-dominant mesh from a dense input with a target face count, optional guide curves for edge flow, density painting, symmetry and feature preservation. | T2 | 5 |
| MB-TOPO-014 | Decimate | Reduces faces to a target count or ratio by error-driven edge collapse, protecting borders, UV seams, sharp edges, materials and vertex colors as chosen, and weighting by an attribute. | T1 | 4 |
| MB-TOPO-015 | Planar decimation | Merges coplanar faces into n-gons within an angle threshold. | T2 | 4 |
| MB-TOPO-016 | Level-of-detail chain | Generates a chain of reduced meshes at target ratios or screen sizes, stored as LOD variants and exported as LOD groups where formats support them. | T2 | 4 |

## Manual retopology

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-TOPO-017 | Draw on surface | Places vertices on a live reference surface and builds quads by clicking, with automatic face creation from four points, strip extension from edges and loop insertion. | T1 | 4 |
| MB-TOPO-018 | Strip and patch tools | Extends a border by a strip of quads with the mouse, and fills a bounded region with a quad patch of chosen density. | T2 | 4 |
| MB-TOPO-019 | Retopology relax | Relaxes the new mesh while keeping it constrained to the reference surface. | T1 | 4 |
| MB-TOPO-020 | Retopology display | Shows the new mesh offset above the reference with a configurable color, opacity and hidden-line mode. | T1 | 4 |
| MB-TOPO-021 | Contour tools | Draws strokes across the reference to create edge rings and loops along limbs or cylinders, with count and spacing. | T2 | 5 |
| MB-TOPO-022 | Project onto reference | Projects the vertices of any mesh onto a reference surface with distance limit, keeping borders. | T1 | 4 |

## Checks and fixes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-TOPO-023 | Mesh check | Finds non-manifold edges and vertices, lamina faces, zero-area faces, zero-length edges, concave and non-planar faces, n-gons, triangles, flipped normals, holes, overlapping vertices and self-intersections; each finding selects its components. | T1 | 4 |
| MB-TOPO-024 | Automatic fixes | Fixes selected finding types (merge, delete degenerate, triangulate concave, unify normals, fill holes) in one command and reports what changed. | T1 | 4 |
| MB-TOPO-025 | Watertight and print check | Checks closedness, wall thickness below a minimum, and inverted shells for 3D printing, and exports a report. | T2 | 4 |
| MB-TOPO-026 | Loose geometry removal | Deletes isolated vertices, edges without faces and shells below a face count or volume. | T1 | 4 |
| MB-TOPO-027 | Check presets | Saves combinations of checks with thresholds as named presets (for example game export, subdivision, print) usable from the CLI. | T2 | 4 |
