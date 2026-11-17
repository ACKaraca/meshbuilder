# UV editing

Texture coordinates: projections, seams, unfolding, layout and packing, texel density and UDIM tiles,
in a dedicated UV editor and as procedural modifiers.

## Domain objects

- **UV map**: a named face-corner attribute of two floats; a mesh holds any number of UV maps, one of
  which is active for display and one marked as the render default.
- **Seam**: an edge flag marking where UV shells are cut.
- **UV shell**: a connected region in UV space.
- **UDIM tile**: a unit square of UV space addressed by tile number.

## UV editor

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-001 | UV editor | Shows UVs of selected meshes in a 2D view with vertex, edge, face and shell modes, the same selection tools as the viewport, and selection synchronized with 3D components. | T1 | 4 |
| MB-UV-002 | UV transforms | Moves, rotates, scales, flips and mirrors UVs with manipulators, numeric entry, pivot choices and snapping to grid, pixels, vertices and tile edges. | T1 | 4 |
| MB-UV-003 | Background images | Displays an assigned texture, a checker pattern or a grid in the editor, with tiling and brightness. | T1 | 4 |
| MB-UV-004 | Distortion display | Colors faces by area and angle distortion in the UV editor and in the 3D viewport. | T1 | 4 |
| MB-UV-005 | Shell boundaries | Draws shell borders and seam edges distinctly in both views, and highlights overlapping and flipped UVs. | T1 | 4 |
| MB-UV-006 | Multiple UV maps | Creates, renames, deletes, copies between and reorders UV maps; selects the active and render map per mesh. | T1 | 4 |

## Projections and seams

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-007 | Projections | Projects UVs planar, box (triplanar), cylindrical, spherical, from view and from camera, with a gizmo, fit to bounds and real-world size. | T1 | 4 |
| MB-UV-008 | Seam editing | Marks and clears seams on selected edges, from sharp edges, from material boundaries or from existing shell borders. | T1 | 4 |
| MB-UV-009 | Automatic seams | Places seams automatically to balance distortion against seam length, with options to hide seams in concave or occluded regions. | T2 | 4 |
| MB-UV-010 | Cut and sew | Cuts UVs along selected edges and sews shells along shared edges, moving the smaller shell to the larger. | T1 | 4 |

## Unfolding

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-011 | Unfold | Unfolds shells with a conformal or area-preserving solver, with pinned UVs respected and iterations configurable. | T1 | 4 |
| MB-UV-012 | Optimize | Relaxes UVs to reduce distortion without changing shell borders, globally or on a selection. | T1 | 4 |
| MB-UV-013 | Straighten | Straightens selected UV edge chains into lines and aligns quad grids to rectangles. | T2 | 4 |
| MB-UV-014 | Pinning | Pins UVs so unfold and optimize keep them fixed. | T1 | 4 |
| MB-UV-015 | Peel along loop | Unfolds a strip-shaped region along a picked edge loop into a straight band. | T2 | 4 |
| MB-UV-016 | Unwrap from mesh state | Unfolds using the mesh as it is at a chosen stack level, so UVs computed on an unbent or unsubdivided mesh carry through deformations. | T2 | 4 |

## Layout and packing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-017 | Pack | Packs shells into the unit square or a set of UDIM tiles with padding in pixels at a target resolution, rotation steps, optional mirroring and scale normalization. | T1 | 4 |
| MB-UV-018 | Orient shells | Rotates shells to align their longest edge or a picked edge to an axis. | T1 | 4 |
| MB-UV-019 | Align and distribute | Aligns UVs or shells to minimum, center or maximum, and distributes shells evenly. | T1 | 4 |
| MB-UV-020 | Stack similar | Stacks shells with matching topology on top of each other, and unstacks them for unique texturing. | T2 | 4 |
| MB-UV-021 | Grouped packing | Packs shells grouped by material, object or selection set into separate regions or tiles. | T2 | 4 |

## Texel density and UDIM

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-022 | Texel density | Measures texels per document unit for shells at a texture resolution, sets a target density on shells, and matches density from a picked shell. | T1 | 4 |
| MB-UV-023 | UDIM tiles | Displays and edits UVs across UDIM tiles, moves shells between tiles, shows tile numbers and loads tiled textures by pattern in the background. | T1 | 4 |
| MB-UV-024 | Tile assignment | Assigns shells to tiles by object, material or selection set, preserving relative scale. | T2 | 4 |

## Transfer

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-UV-025 | Copy and transfer UVs | Copies UVs between meshes with the same topology by component identifiers, and transfers between different topologies by surface projection. | T1 | 4 |
| MB-UV-026 | UV snapshot | Exports the UV layout as a PNG or SVG image with chosen resolution, line color and fill. | T2 | 4 |
