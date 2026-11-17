# Vertex data and attributes

The attribute system that carries all per-element data (positions, normals, colors, UVs, weights,
creases, custom data) through modeling, modifiers, graphs and interchange, and the tools that create,
inspect, paint and transfer it.

## Domain objects

- **Attribute**: a named, typed array on a domain (point, edge, face, face corner, curve, instance,
  voxel). Types: bool, int, float, 2/3/4-vectors, color (linear float or 8-bit sRGB), quaternion,
  matrix, string.
- **Built-in attributes**: position, component identifier, normal, crease, bevel weight, material
  index, UV maps, seam and sharp flags; reserved names that cannot be retyped.
- **Attribute flag**: marks an attribute for export, render, or interpolation behavior.

## Attribute management

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ATTR-001 | Named attributes | Creates, renames, retypes, converts domain and deletes attributes on any geometry; built-in names are protected. Domain conversion uses defined averaging or sampling rules. | T1 | 4 |
| MB-ATTR-002 | Attribute propagation | Every built-in operation, modifier and graph node carries all attributes through to its output, interpolating new elements by the attribute's interpolation rule. | T1 | 4 |
| MB-ATTR-003 | Attribute spreadsheet | Shows attributes of the selection or evaluated geometry in a virtualized table per domain, filterable to selected components; edits to base data are commands. | T1 | 4 |
| MB-ATTR-004 | Attribute visualization | Displays any attribute in the viewport as colors, ramps, vectors or text labels, with range mapping. | T1 | 4 |
| MB-ATTR-005 | Set attribute values | Sets attribute values on selected components numerically, by expression or by copying from the active component. | T1 | 4 |
| MB-ATTR-006 | Render and export flags | Marks attributes to be passed to the renderer through the Arc Scene as primvars and to be written by exporters that support custom attributes. | T2 | 4 |

## Normals

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ATTR-007 | Smooth and hard edges | Marks edges as smooth or hard, sets them by angle threshold, and derives vertex normals that split at hard edges. | T1 | 4 |
| MB-ATTR-008 | Custom normals | Stores explicit per-corner normals, edits them by direction, average, copy and paste, and locks them against recomputation. | T1 | 4 |
| MB-ATTR-009 | Weighted normals | Computes normals weighted by face area, corner angle or face strength, with a keep-sharp option, for hard-surface shading. | T2 | 4 |
| MB-ATTR-010 | Normal transfer | Transfers normals from a target surface (for example a sphere or the unbeveled source) to selected corners by nearest point or projection. | T2 | 4 |
| MB-ATTR-011 | Tangent space | Computes tangents with the MikkTSpace convention per UV map for export and baking. | T1 | 4 |
| MB-ATTR-012 | Normal display | Displays face, vertex and corner normals with length and color settings. | T1 | 4 |

## Colors and weights

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ATTR-013 | Color attributes | Creates point or corner color attributes in linear float or 8-bit sRGB with alpha; sets the active display and render color attribute. | T1 | 4 |
| MB-ATTR-014 | Attribute paint | Paints colors and scalar attributes with a brush (set, add, multiply, blur, smudge), falloff, symmetry, pressure response, and masking by selection or face sets. Skin weights use this brush with weight-specific options in 21-skinning-and-deformation. | T1 | 4 |
| MB-ATTR-015 | Fill and gradients | Fills selected components with a value or a linear or radial gradient between two picked points. | T2 | 4 |
| MB-ATTR-016 | Derived masks | Generates scalar attributes from geometry: curvature, cavity, ambient occlusion, edge distance, thickness and direction to a point. | T2 | 4 |
| MB-ATTR-017 | Face sets and material IDs | Assigns integer groups to faces, used for selection, masking and material assignment. | T1 | 4 |

## Transfer

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-ATTR-018 | Attribute transfer | Transfers chosen attributes (positions, normals, UVs, colors, weights, custom) from a source mesh to a target by nearest point, nearest surface, ray along normal or UV space, with blending and a weight mask. | T1 | 4 |
| MB-ATTR-019 | Topology-matched copy | Copies attributes between meshes with identical topology by component identifier or index order, reporting mismatches. | T1 | 4 |
| MB-ATTR-020 | Shape transfer | Copies vertex positions from a matching-topology mesh to the target with a blend factor, for corrective and variant shapes. | T2 | 4 |
