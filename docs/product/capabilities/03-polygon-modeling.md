# Polygon modeling

Direct editing of polygon meshes: primitives, creation of faces, component operations on vertices,
edges, faces and shells, cutting, bevels and mesh-level structure edits. Every operation is a command
that records its parameters, so it can be redone with new values until the next edit, and every
operation is also available as a modifier and a graph node (see
[05-procedural-modifiers.md](05-procedural-modifiers.md)).

## Domain objects

- **Mesh**: half-edge polygon mesh with n-gons, holes in faces disallowed, non-manifold
  configurations representable and flagged.
- **Component identifier**: stable 64-bit identifier of each vertex, edge and face, preserved
  across operations wherever the component survives.
- **Operation record**: the last operation's parameters, editable in the redo panel.

## Primitives and creation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-001 | Mesh primitives | Creates plane, box, sphere (UV, geodesic, quad-ball), cylinder, cone, torus, tube, capsule, pyramid, prism, helix, platonic solids, rounded box and text meshes with segment, radius and cap parameters. Parameters stay editable while the primitive is the bottom of its stack. | T1 | 4 |
| MB-POLY-002 | Interactive creation | Draws primitives by clicks and drags on the grid, a construction plane or a surface, with snapping and numeric entry for each dimension. | T1 | 4 |
| MB-POLY-003 | Create polygon | Creates faces by placing vertices in sequence, snapping to existing vertices, and appends faces to open borders. | T1 | 4 |
| MB-POLY-004 | Fill hole | Caps selected or all open borders with one n-gon, a triangle fan, or a quad grid fill that matches border edge counts where possible. | T1 | 4 |
| MB-POLY-005 | Bridge | Connects two edge borders or face selections with a strip of faces, with segment count, twist, taper, smoothness and a profile curve; handles unequal edge counts by distributing. | T1 | 4 |

## Vertex operations

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-006 | Merge vertices | Merges selected vertices to their center, to the first or last selected, or by distance threshold; reports the number merged. | T1 | 4 |
| MB-POLY-007 | Target weld | Drags a vertex onto another vertex (or an edge onto an edge) to weld them interactively. | T1 | 4 |
| MB-POLY-008 | Break and rip | Splits a vertex into one vertex per adjacent face, or rips selected components away from their neighbors to open a hole. | T1 | 4 |
| MB-POLY-009 | Vertex chamfer | Replaces a vertex with a face whose corners lie on each adjacent edge at a given distance. | T1 | 4 |
| MB-POLY-010 | Dissolve vertex | Removes vertices and merges adjacent faces, keeping the surface shape where vertices are collinear. | T1 | 4 |
| MB-POLY-011 | Connect vertices | Inserts edges between selected vertices across shared faces. | T1 | 4 |

## Edge operations

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-012 | Insert edge loop | Inserts one or more edge loops across a ring with interactive placement, even spacing, relative or absolute distance from one side, and preview before commit. | T1 | 4 |
| MB-POLY-013 | Connect edges | Inserts N edges across a selected edge ring with pinch and slide parameters. | T1 | 4 |
| MB-POLY-014 | Offset edge loop | Inserts loops on both sides of a selected loop at a given distance. | T1 | 4 |
| MB-POLY-015 | Dissolve edges and loops | Removes edges and merges adjacent faces; removing a full loop also removes the vertices it leaves with valence two. | T1 | 4 |
| MB-POLY-016 | Collapse | Collapses selected edges or faces to single vertices at their centers. | T1 | 4 |
| MB-POLY-017 | Spin and turn edge | Rotates an edge to connect the next vertices of its two faces; on triangles, flips the diagonal. | T1 | 4 |
| MB-POLY-018 | Split and detach edges | Splits geometry along selected edges, creating open borders without separating shells. | T1 | 4 |
| MB-POLY-019 | Edge extrude | Extrudes edges or borders into new faces along a direction or the surface tangent. | T1 | 4 |
| MB-POLY-020 | Edges to curve | Creates a curve object from a selected edge loop or chain, optionally smoothed. | T2 | 4 |

## Face operations

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-021 | Extrude faces | Extrudes faces as a region or individually along region normal, per-face normal or an axis, with offset, divisions, taper, twist and thickness; an interactive handle sets the distance. | T1 | 4 |
| MB-POLY-022 | Inset | Insets faces as a region or individually with thickness and depth, keeping boundaries even. | T1 | 4 |
| MB-POLY-023 | Face bevel | Insets and extrudes faces in one step with outline amount and height. | T1 | 4 |
| MB-POLY-024 | Extrude along curve | Extrudes faces along a picked curve with segments, taper and twist curves, aligning to the curve's frame. | T2 | 4 |
| MB-POLY-025 | Hinge extrude | Extrudes faces by rotating them around a picked edge through an angle with segments. | T2 | 4 |
| MB-POLY-026 | Poke and wedge | Pokes faces with a center vertex connected to every corner, or wedges faces by revolving them around a picked edge. | T2 | 4 |
| MB-POLY-027 | Flip and conform | Flips face orientation, and conforms orientation of a shell to the majority or to outward-facing. | T1 | 4 |
| MB-POLY-028 | Triangulate and quadrangulate | Triangulates selected faces by shortest diagonal or by beauty criterion, and joins triangle pairs into quads under angle and planarity thresholds. | T1 | 4 |
| MB-POLY-029 | Subdivide faces | Divides faces linearly into a grid (quads) or into triangles by a level, without smoothing. | T1 | 4 |
| MB-POLY-030 | Make planar | Projects selected components onto their best-fit plane or onto a picked axis plane. | T2 | 4 |

## Bevel and chamfer

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-031 | Edge bevel | Bevels edges with width (absolute, percent, by depth), segments, profile shape (convex to concave curve or custom profile), miter patterns at corners, and clamping that prevents overlaps. | T1 | 4 |
| MB-POLY-032 | Bevel weights | Bevels only edges whose stored bevel weight exceeds a threshold, with width scaled by weight, so hard-surface edges are marked once and beveled procedurally. | T2 | 4 |
| MB-POLY-033 | Shape-following bevel | Bevels an edge network by offsetting along the surface rather than along adjacent faces, producing even-width bevels and clean quad-dominant corners on geometry with irregular topology such as boolean results. | T3 | 4 |

## Cutting

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-034 | Multi-cut | Draws cuts across faces vertex to vertex, edge to edge or mid-face, snapping to vertices, midpoints and angle increments; with a modifier key it inserts a loop instead. | T1 | 4 |
| MB-POLY-035 | Plane slice | Slices the mesh or selection with a plane positioned by manipulator, optionally deleting one side and capping the cut. | T1 | 4 |
| MB-POLY-036 | Knife project | Cuts the mesh along the projection of a curve or another mesh's outline from the view or an axis. | T2 | 4 |

## Shells and mesh structure

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-037 | Combine and separate | Combines meshes into one object, and separates by shells, by material or by selection into new objects, preserving attributes and materials. | T1 | 4 |
| MB-POLY-038 | Detach and duplicate faces | Detaches selected faces into a separate shell or object, or duplicates them in place. | T1 | 4 |
| MB-POLY-039 | Delete components | Deletes vertices, edges or faces with the choice to delete only faces, or also orphaned edges and vertices. | T1 | 4 |
| MB-POLY-040 | Mirror geometry | Mirrors the mesh across an axis plane with merge of the seam within a threshold, optionally deleting geometry past the plane first. | T1 | 4 |
| MB-POLY-041 | Symmetrize | Copies one side of a mesh onto the other across a plane or topological symmetry, reporting unmatched vertices. | T2 | 4 |
| MB-POLY-042 | Hide components | Hides selected components from display and selection without deleting them, with unhide all. | T1 | 4 |

## Freeform and relax

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-POLY-043 | Relax and smooth vertices | Relaxes vertex positions toward an even distribution, optionally constrained to the original surface and with borders pinned. | T1 | 4 |
| MB-POLY-044 | Push, pull and shift brushes | Provides modeling brushes (push/pull along normal, move, relax, flatten, pinch) with radius, strength and falloff for quick freeform edits on meshes of any density. High-detail sculpting is specified in 25-sculpting. | T2 | 4 |
| MB-POLY-045 | Redo panel | Shows the parameters of the last operation and re-executes it with changed values as a replacement of the same transaction. | T1 | 4 |
| MB-POLY-046 | Repeat last | Repeats the last modeling command with its last parameters on the current selection. | T1 | 4 |
