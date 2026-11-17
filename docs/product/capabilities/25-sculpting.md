# Sculpting

This area specifies freeform sculpting in MeshBuilder: brushes, symmetry, multiresolution subdivision levels, sculpt layers, dynamic topology and voxel remeshing, masks and face sets, pose and cloth brushes, retopology, detail projection and handling of very high polygon counts. Sculpting works on the same mesh objects as polygon modeling, so a sculpted model can return to the modifier stack, be rigged and be baked without conversion.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Sculpt Mesh | Base mesh, active level, level count, displacement data per level, sculpt mode (levels or dynamic) | A mesh object in the scene; owns Levels, Layers, Masks and Face Sets |
| Subdivision Level | Level index, vertex count, displacement from the subdivided lower level | Member of a Sculpt Mesh; edits at one level propagate to the others |
| Sculpt Layer | Name, per-vertex offsets per level, strength, mask, visibility, group | Stacked on a Sculpt Mesh; non-destructive contribution to the shape |
| Brush | Type, size, strength, falloff curve, stroke spacing, stamp, stencil, pressure mappings, symmetry override | A preset in the brush library; applied by strokes |
| Stamp | Image alpha or height image, spacing, rotation, scatter | Used by Brushes |
| Stencil | Image, transform on screen, opacity, tiling | Modulates stroke strength across screen space |
| Mask | Per-vertex value 0 to 1, name | Stored on a Sculpt Mesh; multiple masks may be stacked |
| Face Set | Face membership, color, visibility | Partition of faces used to hide, isolate and limit tools |
| Pose Chain | Segments, pivots, falloff, rigidity | Temporary chain defined by a pose brush for bending and rotating parts |
| Cloth Brush State | Particle simulation over the brush area, damping, gravity, collisions | Temporary simulation used to form folds |
| Retopology Surface | Target Sculpt Mesh, user-drawn faces, guide curves, density map, symmetry | Produces a new low-polygon mesh conforming to a high-resolution source |
| Detail Transfer | Source mesh, target mesh, projection mode, maps | Moves surface detail between meshes |

## Brushes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-001 | Core brush set | Provides draw, clay, smooth, grab, inflate, pinch, flatten, crease, scrape, fill, layer, nudge, snake-hook, bulge, relax and erase brushes; each moves vertices along a stated direction (normal, view, average normal or fixed) with a falloff. | T1 | 5 |
| MB-SCULPT-002 | Brush parameters | Size, strength, spacing, accumulation, normal radius, falloff curve and invert are parameters; size may be in screen pixels or world units, and the cursor shows the area of effect. | T1 | 5 |
| MB-SCULPT-003 | Falloff curves | A falloff is a preset (smooth, sphere, root, sharp, constant) or a custom curve drawn in an editor, and a brush may save its own curve. | T1 | 5 |
| MB-SCULPT-004 | Pressure and tablet response | Pen pressure, tilt and speed map to size and strength through editable response curves; mouse input uses constant values. | T1 | 5 |
| MB-SCULPT-005 | Stroke smoothing and stabilization | A stroke can be smoothed by a lazy-rope distance or by sample averaging, and a stroke may follow a drawn curve or a line constraint. | T1 | 5 |
| MB-SCULPT-006 | Brush library | Brushes are saved with all parameters in a library with icons and categories, imported and exported as files, and reachable from a radial selector. | T1 | 5 |
| MB-SCULPT-007 | Stamps | A stamp uses an image or height image as the brush shape with spacing, rotation, random scatter and mirroring, producing repeated imprints or a dragged shape. | T1 | 5 |
| MB-SCULPT-008 | Stencils | A stencil places an image on screen and modulates stroke strength by its value, with move, scale, rotate and flip controls and an opacity setting. | T1 | 5 |
| MB-SCULPT-009 | Vector displacement stamps | A stamp may carry a three-component displacement image so overhanging features stamp onto the surface as a full 3D offset. | T2 | 5 |
| MB-SCULPT-010 | Surface-aware brushes | Brushes can limit effect to areas connected to the stroke start, to the same face set, or to unmasked areas, so strokes do not cross gaps. | T1 | 5 |
| MB-SCULPT-011 | Drag and curve strokes | Drag, line, curve and lasso strokes apply a brush along a path or inside a shape instead of a freehand stroke. | T2 | 5 |
| MB-SCULPT-012 | Detail-adaptive size | Brush size and strength adapt to local polygon density or to the distance to the surface, so strokes behave consistently across scales. | T3 | 5 |

## Symmetry

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-013 | Axis symmetry | Mirrors strokes across X, Y or Z planes of the object or the world, with a threshold for center vertices. | T1 | 5 |
| MB-SCULPT-014 | Radial symmetry | Repeats strokes around an axis with a count, useful for symmetrical features such as ornaments. | T2 | 5 |
| MB-SCULPT-015 | Topological symmetry | Computes the mirror partner of each vertex from the mesh connectivity rather than position, so symmetric sculpting works on models posed asymmetrically, and the match is validated with a report of vertices without a partner. | T1 | 5 |
| MB-SCULPT-016 | Symmetrize | A command makes the mesh symmetric by copying one side to the other across the plane or the topology, with a choice of direction. | T1 | 5 |

## Multiresolution

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-017 | Subdivision levels | Adds levels by Catmull-Clark subdivision with options for smoothing positions and preserving borders; the active level is changed with commands and shortcuts, and detail at higher levels is retained when a lower level is edited. | T1 | 5 |
| MB-SCULPT-018 | Level management | Higher levels or lower levels can be deleted, and a lower level can be rebuilt from the highest by reverse subdivision; level sizes and memory use are displayed. | T1 | 5 |
| MB-SCULPT-019 | Level-aware editing | A stroke edits only the active level's displacement and is propagated to higher levels; reprojection to match changed lower levels is a command with a tolerance. | T1 | 5 |
| MB-SCULPT-020 | Apply base mesh | A command applies the sculpt of a low level into the base mesh and updates higher levels, so the base follows large-scale changes. | T2 | 5 |
| MB-SCULPT-021 | Subdivision surface consistency | The subdivision used while sculpting is the same as that used for polygon modeling and rendering, so a sculpt evaluated by a renderer matches the viewport. | T1 | 5 |

## Sculpt layers

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-022 | Sculpt layers | Strokes are recorded into a layer as per-vertex offsets relative to the layers below; each layer has a strength slider, visibility, mask and name, and combined layers produce the result. | T1 | 5 |
| MB-SCULPT-023 | Layer management | Layers are renamed, reordered, grouped, duplicated, merged down, baked into the base and deleted, and layer strengths can be keyed to animate a shape change. | T1 | 5 |
| MB-SCULPT-024 | Layer masks and clamping | A layer can carry a mask and a clamp to limit its offsets, and a brush can paint the layer's mask directly. | T2 | 5 |
| MB-SCULPT-025 | Layers to blend shapes | A layer, or a set of layers, can be extracted as a Shape Target for a Blend Shape Set, with layer strength mapped to the target weight. | T1 | 5 |
| MB-SCULPT-026 | Layers across levels | Layers store offsets per level; changing the active level shows the layer at that resolution, and adding a level resamples layers by subdivision. | T2 | 5 |

## Dynamic topology and remeshing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-027 | Dynamic topology | While sculpting, the mesh refines where the brush needs more detail and coarsens where it does not, to a target detail size in screen or world units; the mode is separate from subdivision levels and says so when switched. | T1 | 5 |
| MB-SCULPT-028 | Voxel remesh | Rebuilds the mesh as an evenly sized quad or triangle mesh from a voxel size, optionally preserving masked or face-set borders and sharp edges; the result keeps vertex attributes by projection. | T1 | 5 |
| MB-SCULPT-029 | Quad remesh | Rebuilds the mesh as a quad-dominant mesh with a target face count, edge-flow guides from curves and a symmetry option, for use as a sculpting or retopology base. | T2 | 5 |
| MB-SCULPT-030 | Decimation | Reduces polygon count to a target while preserving silhouette, borders, UV seams and masked areas, and reports the resulting error. | T1 | 5 |
| MB-SCULPT-031 | Attribute preservation | Remeshing and dynamic topology carry masks, face sets, vertex colors and UVs by projection, with a visible warning when UVs are discarded. | T1 | 5 |

## Masking

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-032 | Mask painting | A mask brush adds or removes mask values; masked vertices are protected from sculpt brushes and from transform tools proportionally. | T1 | 5 |
| MB-SCULPT-033 | Mask generation | Masks are generated from shapes (box, lasso, line), cavity, curvature, height, UV, vertex color, face set, or a texture, and combined with operations (add, subtract, intersect, invert). | T1 | 5 |
| MB-SCULPT-034 | Mask refinement | Grow, shrink, sharpen, blur, smooth and extract operations edit a mask; a mask can be converted to a face set, a selection or a new surface. | T1 | 5 |
| MB-SCULPT-035 | Mask extract and extrude | A masked region can be extracted into a new mesh with thickness or extruded in place, keeping borders clean. | T2 | 5 |

## Face sets and visibility

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-036 | Face sets | Face sets partition faces into colored groups created by drawing, by connectivity, by sharp edges, by UV seams, or from a mask, and are used to limit brushes and to isolate regions. | T1 | 5 |
| MB-SCULPT-037 | Hide, isolate and show | Faces are hidden, isolated or shown by face set, selection or box, and hidden faces are excluded from strokes, display and exports of the sculpt state. | T1 | 5 |
| MB-SCULPT-038 | Face set operations | Face sets can be grown, shrunk, merged, split, randomized in color, converted to polygon groups or material slots, and kept through remeshing. | T2 | 5 |

## Pose and cloth brushes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-039 | Pose brush | A drag creates a temporary chain of segments from the stroke start, with falloff and rigidity, and bends, twists or scales the model around the pivots without a rig. | T1 | 5 |
| MB-SCULPT-040 | Transform with falloff | A move, rotate and scale gizmo applies a transformation to the unmasked region with a falloff and a choice of space, and keeps topology. | T1 | 5 |
| MB-SCULPT-041 | Cloth brush | A brush runs a short particle cloth simulation under the cursor to form folds and drapes, with settings for damping, gravity, density, collision with the underlying surface and a stroke duration. | T2 | 5 |
| MB-SCULPT-042 | Boundary and surface tools | Brushes act along mask or face set borders to smooth, bend or offset them, and a surface smoothing brush preserves volume at a stated strength. | T2 | 5 |

## Retopology

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-043 | Manual retopology | Faces are drawn over a high-resolution mesh with vertices constrained to its surface; tools extend a face, bridge, loop-insert, relax and weld, and the target updates as the source is edited. | T1 | 5 |
| MB-SCULPT-044 | Automatic retopology | Produces a low-polygon quad mesh from a high-resolution mesh with a target face count, symmetry, guide curves for edge flow and a density map painted on the source. | T1 | 5 |
| MB-SCULPT-045 | Retopology guides | Curves drawn on the surface act as edge-flow constraints, and a density brush raises or lowers the face size locally. | T2 | 5 |
| MB-SCULPT-046 | Retopology checks | The target is checked for triangles, poles and non-manifold edges, and an overlay shows deviation from the source surface with a configurable tolerance. | T2 | 5 |
| MB-SCULPT-047 | Retopology to UV and bake | A retopologized mesh is passed to UV tools and to baking with the sculpt as the high-resolution source with a single command. | T1 | 5 |

## Detail projection and transfer

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-048 | Project detail | Detail from a source mesh is projected onto a target by ray or closest-point projection and written into the target's levels or a layer, with a distance limit and a blend. | T1 | 5 |
| MB-SCULPT-049 | Subdivision-level projection | A low-resolution mesh with new topology receives the detail of the previous sculpt by projecting each of its subdivision levels, so a retopologized model keeps its sculpted detail. | T1 | 5 |
| MB-SCULPT-050 | Detail transfer between meshes | Detail is transferred as height or vector displacement maps between meshes of different topology or UV layouts and applied back as offsets. | T2 | 5 |

## Scale and performance

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCULPT-051 | Very high polygon counts | Sculpting stays interactive on meshes with tens of millions of polygons using spatial partitioning, chunked updates on the GPU and level-of-detail display during strokes, and the performance target and measured rate are stated in the product's budgets. | T1 | 5 |
| MB-SCULPT-052 | Out-of-core sculpt data | Level and layer data are stored in the document package in chunks and loaded on demand, so a model larger than memory opens and edits the visible region. | T3 | 5 |
| MB-SCULPT-053 | Sculpt undo | Undo records stroke deltas rather than whole meshes and is limited by a configurable memory budget with a visible indicator; exhausted undo falls back to periodic snapshots. | T1 | 5 |
| MB-SCULPT-054 | Multiple sculpt objects | A scene may contain many sculpt meshes handled as a group for display, symmetry and merging, and a selected set can be merged or split with attributes preserved. | T2 | 5 |
| MB-SCULPT-055 | Assisted sculpt operations | An optional assistant proposes a retopology density map or a symmetry plane from a mesh, shown as an editable preview before it is applied. | T3 | 5 |
