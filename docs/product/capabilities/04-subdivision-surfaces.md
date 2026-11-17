# Subdivision surfaces

Smooth limit surfaces from polygon control meshes: subdivision schemes, creasing, preview and render
levels, and the controls that keep viewport, renderer and interchange results identical.

## Domain objects

- **Subdivision settings**: scheme, viewport level, render level, boundary and face-varying rules,
  stored on a mesh or a subdivision modifier.
- **Crease data**: per-edge and per-vertex sharpness values stored as mesh attributes.
- **Crease set**: named group of edges sharing one sharpness value.

## Schemes and levels

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SUBD-001 | Catmull-Clark subdivision | Subdivides quad-dominant meshes with the Catmull-Clark scheme, results matching the open subdivision specification used by renderers and USD. | T1 | 4 |
| MB-SUBD-002 | Loop and bilinear schemes | Subdivides triangle meshes with the Loop scheme and any mesh with bilinear (linear) subdivision. | T2 | 4 |
| MB-SUBD-003 | Viewport and render levels | Stores separate viewport and render levels; the Arc Scene receives the control mesh plus scheme and level so the renderer subdivides itself. | T1 | 4 |
| MB-SUBD-004 | Subdivision preview toggle | Switches any mesh between control-mesh display, smooth preview and both (cage over smooth) with one key, without modifying data. | T1 | 4 |
| MB-SUBD-005 | GPU subdivision | Evaluates subdivision for display on the GPU with adaptive feature refinement, keeping the control mesh as the only CPU data; falls back to CPU when unavailable. | T2 | 4 |
| MB-SUBD-006 | Adaptive level by screen size | Picks the display level per object from projected size and a quality target, within the stored maximum. | T3 | 5 |
| MB-SUBD-007 | Apply subdivision | Converts the subdivided result at a chosen level into a regular mesh, carrying over UVs, attributes and creases. | T1 | 4 |

## Creasing and boundaries

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SUBD-008 | Edge and vertex creases | Assigns sharpness values (zero to infinite) to edges and vertices; fractional values produce semi-sharp transitions over subdivision levels. | T1 | 4 |
| MB-SUBD-009 | Crease tool | Paints or drags crease values on edges and vertices in the viewport, displaying sharpness as line thickness or color. | T1 | 4 |
| MB-SUBD-010 | Crease sets | Groups edges into named crease sets whose shared value can be changed in one edit. | T2 | 4 |
| MB-SUBD-011 | Boundary interpolation | Selects boundary rules (edges only, edges and corners, none) per mesh. | T1 | 4 |
| MB-SUBD-012 | Face-varying interpolation | Selects how UVs and other face-varying attributes subdivide (linear, boundaries only, corners plus variants, all smooth) per attribute. | T2 | 4 |
| MB-SUBD-013 | Holes | Marks faces as holes that are removed from the limit surface while still influencing neighbors. | T2 | 4 |
| MB-SUBD-014 | Control cage editing | Edits the control mesh while showing the limit surface, with components optionally picked on the limit surface and mapped to the cage. | T1 | 4 |
| MB-SUBD-015 | Limit surface evaluation | Evaluates positions, normals and tangents on the limit surface for snapping, scattering, placement and baking. | T2 | 4 |
| MB-SUBD-016 | Topology checks for subdivision | Reports extraordinary vertices, triangles, n-gons and non-manifold edges that affect smoothness and highlights them in the viewport. | T2 | 4 |
