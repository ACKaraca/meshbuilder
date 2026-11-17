# Procedural modifiers

The non-destructive modifier stack: an ordered list of operations on an object's data that
re-evaluates whenever its input or parameters change. The stack is a linear view of the object's
geometry graph (see [06-geometry-node-graphs.md](06-geometry-node-graphs.md)); every modifier is a
node and every polygon operation in [03-polygon-modeling.md](03-polygon-modeling.md) is available as
a modifier. Deformers used in rigs (nonlinear, lattice, wrap, cluster, smoothing deformers) are
specified in 21-skinning-and-deformation and enter the stack through the same mechanism.

## Domain objects

- **Modifier**: a geometry graph node with parameters, a viewport toggle, a render toggle and an
  optional component selection input.
- **Modifier stack**: the ordered chain of modifiers between an object's base data and its
  evaluated result.
- **Recorded edit**: a modifier that stores direct component edits keyed by component identifiers.

## Stack mechanics

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOD-001 | Modifier stack | Adds, removes, reorders by drag, renames, enables and disables modifiers, with separate viewport and render enable flags. Result updates after each change through the evaluation graph. | T1 | 4 |
| MB-MOD-002 | Show result at level | Displays the evaluated result at a chosen stack entry while editing a lower one, or always the final result, per object. | T1 | 4 |
| MB-MOD-003 | Apply and collapse | Applies a modifier into the base data, or collapses the stack up to a chosen entry, as one undoable command; collapsing an instanced stack first asks whether to make the object unique. | T1 | 4 |
| MB-MOD-004 | Copy, paste and instance modifiers | Copies modifiers between objects, or instances a modifier so several stacks share its parameters. | T1 | 4 |
| MB-MOD-005 | Component selection inputs | Restricts a modifier to a stored component selection set, a selection produced by a selection modifier, or an attribute weight; selections bind to component identifiers. | T1 | 4 |
| MB-MOD-006 | Recorded edits | Records direct component edits (moves, operations) as a stack entry keyed by component identifiers, so edits survive upstream changes for every component that still exists; edits whose targets disappear are reported, not silently dropped. | T1 | 4 |
| MB-MOD-007 | Stack presets | Saves a modifier chain with parameters as a named preset and applies it to other objects. | T2 | 4 |
| MB-MOD-008 | Open as graph | Opens any stack in the geometry graph editor; a stack edited into a branching graph shows as one graph entry in the stack view. | T1 | 4 |
| MB-MOD-009 | Modifier parameter animation | Numeric modifier parameters are animatable and can be driven by expressions or other properties. | T1 | 4 |
| MB-MOD-010 | Stack evaluation status | Shows per-modifier evaluation time, warnings and errors (for example, an input with no faces) next to each entry. | T2 | 4 |

## Selection modifiers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOD-011 | Selection by components | Passes a stored component selection up the stack. | T1 | 4 |
| MB-MOD-012 | Selection by volume | Selects components inside a box, sphere, cylinder or mesh volume, with soft falloff weights. | T2 | 4 |
| MB-MOD-013 | Selection by rule | Selects components by normal direction, attribute range, material, texture value or field value, with soft falloff. | T2 | 4 |

## Generators

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOD-014 | Array | Repeats the input linearly, radially, along a curve or on a grid, by count or fit length, with offset per copy, random variation and optional merge of touching ends. Can output instances or merged geometry. | T1 | 4 |
| MB-MOD-015 | Surface scatter | Distributes copies or instances of objects over a surface by density, attribute weight or texture, with minimum spacing, random rotation, scale and alignment to normal, and a seed. | T2 | 4 |
| MB-MOD-016 | Pack along curve | Packs copies of objects along a curve end to end by their bounds, with gap, random order and fit modes. | T3 | 4 |
| MB-MOD-017 | Mirror | Mirrors across one to three axis planes of a frame, with seam merge and bisect. | T1 | 4 |
| MB-MOD-018 | Symmetry | Replaces one side with a mirrored copy of the other across a plane, with weld threshold and flip. | T1 | 4 |
| MB-MOD-019 | Shell | Adds thickness inward, outward or centered, with even thickness, rim faces and separate materials for inner and rim faces. | T1 | 4 |
| MB-MOD-020 | Bevel modifier | Applies edge or vertex bevel procedurally by angle, by bevel weight or by selection, with all bevel options of MB-POLY-031. | T1 | 4 |
| MB-MOD-021 | Wireframe | Converts edges into tubes or strips of given thickness. | T2 | 4 |
| MB-MOD-022 | Lathe | Revolves curves or open meshes around an axis with angle and segments. | T1 | 4 |
| MB-MOD-023 | Extrude modifier | Extrudes curves or faces by a distance along a chosen direction, with caps and segments. | T1 | 4 |
| MB-MOD-024 | Remesh modifier | Rebuilds the input as an even triangle, quad or voxel mesh at a target size; see MB-TOPO for remeshing behavior. | T2 | 4 |
| MB-MOD-025 | Boolean modifier | Combines the object with operand objects or collections by union, difference, intersection or cut; operands stay editable. Behavior follows MB-TOPO-001 to MB-TOPO-008. | T1 | 4 |

## Shape modifiers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-MOD-026 | Smooth and relax | Smooths positions by Laplacian or tangential relaxation with iterations, strength, pinned borders and a weight input. | T1 | 4 |
| MB-MOD-027 | Displace | Offsets positions along normals, an axis or a vector field from a texture, a noise or an attribute, with strength and midlevel. | T1 | 4 |
| MB-MOD-028 | Noise | Offsets positions by fractal noise types (gradient, cellular, ridged, turbulence) with scale, octaves, seed, animation over time and tiling. | T2 | 4 |
| MB-MOD-029 | Spherify and cast | Blends positions toward a sphere, cylinder or box shape by a factor. | T2 | 4 |
| MB-MOD-030 | Push and inflate | Moves vertices along their normals by a distance. | T1 | 4 |
| MB-MOD-031 | Shrinkwrap | Projects vertices onto a target surface by nearest point, along normals or along an axis, with offset and keep-above-surface option. | T2 | 4 |
| MB-MOD-032 | Weld and clean | Merges vertices within a distance and removes degenerate faces and loose components procedurally. | T1 | 4 |
| MB-MOD-033 | Slice and cap | Cuts geometry with one or more planes, keeping either side and optionally capping. | T2 | 4 |
| MB-MOD-034 | Decimate modifier | Reduces face count procedurally with the behavior of MB-TOPO-014. | T1 | 4 |
| MB-MOD-035 | Subdivision modifier | Applies subdivision with the settings of [04-subdivision-surfaces.md](04-subdivision-surfaces.md) as a stack entry, so modifiers can sit above it. | T1 | 4 |
| MB-MOD-036 | Normals modifiers | Sets normals procedurally: auto-smooth by angle, weighted by face area or angle, from a target object, or face-oriented; see MB-ATTR for normal data. | T1 | 4 |
| MB-MOD-037 | UV modifiers | Projects UVs (planar, box, cylindrical, spherical, camera) with a gizmo, transforms UVs, and runs unwrap procedurally; see [09-uv-editing.md](09-uv-editing.md). | T1 | 4 |
| MB-MOD-038 | Attribute modifiers | Creates, transfers, remaps and combines attributes procedurally; see [10-vertex-data-and-attributes.md](10-vertex-data-and-attributes.md). | T2 | 4 |
| MB-MOD-039 | Graph modifier | Inserts a geometry graph asset into the stack with its published parameters as modifier parameters. | T1 | 4 |
| MB-MOD-040 | Plug-in modifiers | Hosts modifiers implemented by sandboxed plug-ins with the same stack behaviors as built-in modifiers. | T2 | 5 |
