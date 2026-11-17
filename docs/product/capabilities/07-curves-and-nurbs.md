# Curves, splines and NURBS

Curve objects (polylines, Bezier splines, NURBS curves, strand curves), NURBS surfaces, and the
operations that turn curves into geometry. Curves are also inputs to modifiers, graphs, constraints
and motion paths.

## Domain objects

- **Curve set**: one object holding many curves of one or mixed types (poly, Bezier, NURBS), with
  per-point and per-curve attributes.
- **Strand curves**: a curve set specialized for many short curves attached to a surface (hair,
  fibers), carrying root positions and surface coordinates.
- **NURBS surface**: rational B-spline surface with degrees, knots, weights and optional trims.

## Curve creation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CURVE-001 | Curve drawing | Draws polylines, Bezier splines (corner, smooth, symmetric and free handles) and NURBS curves by control points or edit points, with snapping and construction planes. | T1 | 4 |
| MB-CURVE-002 | Freehand curves | Draws curves freehand with a pen or mouse, fitted to a tolerance, on a plane or projected onto a surface. | T2 | 4 |
| MB-CURVE-003 | Curve primitives | Creates line, rectangle (with corner radius), circle, ellipse, arc, polygon, star, helix and spiral curves with editable parameters. | T1 | 4 |
| MB-CURVE-004 | Text curves | Creates outline curves from text using installed or embedded fonts, with size, spacing, alignment and per-character offset. | T2 | 4 |
| MB-CURVE-005 | Curves from geometry | Creates curves from mesh edge loops, mesh boundaries, plane sections of meshes, NURBS isoparms and intersections. | T2 | 4 |

## Curve editing

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CURVE-006 | Point editing | Moves control points and handles, changes point types, inserts and deletes points while preserving shape where possible. | T1 | 4 |
| MB-CURVE-007 | Open, close, reverse | Opens or closes curves, reverses direction and sets the start point of a closed curve. | T1 | 4 |
| MB-CURVE-008 | Join and split | Joins curves end to end within a tolerance and splits curves at points or parameters. | T1 | 4 |
| MB-CURVE-009 | Fillet and chamfer | Fillets corners with a fixed radius or chamfers them with a distance, per corner or all corners. | T1 | 4 |
| MB-CURVE-010 | Offset | Offsets planar curves by a distance with round, miter or bevel joins, removing self-intersections. | T1 | 4 |
| MB-CURVE-011 | Trim and extend | Trims curves at intersections with other curves and extends ends to a boundary. | T2 | 4 |
| MB-CURVE-012 | Planar curve booleans | Unions, subtracts and intersects closed planar curves into new closed curves. | T2 | 4 |
| MB-CURVE-013 | Rebuild and resample | Rebuilds a curve with a given point count or degree, or resamples by count or length; reports the maximum deviation. | T1 | 4 |
| MB-CURVE-014 | Smooth and simplify | Smooths point positions and removes points within a tolerance. | T2 | 4 |
| MB-CURVE-015 | Project onto surface | Projects curves onto a mesh or NURBS surface along the view, an axis or normals. | T2 | 4 |
| MB-CURVE-016 | Convert curve types | Converts between poly, Bezier and NURBS representations with exact conversion where mathematically possible and fitting otherwise. | T1 | 4 |
| MB-CURVE-017 | Curve measurement | Reports length, closedness, planarity and curvature, and shows curvature combs. | T2 | 4 |

## Curve to geometry

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CURVE-018 | Renderable thickness | Gives curves a round or rectangular cross section with sides, width, rotation and UVs, displayed and exported as a mesh or as curves to formats that support them. | T1 | 4 |
| MB-CURVE-019 | Sweep | Sweeps a profile curve along a path with scale and twist curves, banking, profile alignment and mitered or rounded corners, producing a mesh with UVs. | T1 | 4 |
| MB-CURVE-020 | Profile library | Provides parametric profiles (round, box, angle, channel, tee, I-section, pipe) for sweeps. | T2 | 4 |
| MB-CURVE-021 | Loft | Lofts a mesh or NURBS surface through an ordered set of section curves with matching, closing and segment controls. | T1 | 4 |
| MB-CURVE-022 | Planar fill | Fills closed planar curves with faces, holes taken from nested curves. | T1 | 4 |
| MB-CURVE-023 | Bevel extrude | Extrudes closed curves with front and back bevel profiles, for logos and lettering. | T2 | 4 |

## NURBS surfaces

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CURVE-024 | NURBS surface primitives | Creates NURBS planes, spheres, cylinders, cones and tori with degree and span parameters. | T2 | 4 |
| MB-CURVE-025 | Surfaces from curves | Builds surfaces by revolve, extrude, loft, one-rail and two-rail sweep, boundary (three or four curves) and planar trim. | T2 | 4 |
| MB-CURVE-026 | Surface editing | Edits control vertices and weights, inserts isoparms, rebuilds, attaches and detaches surfaces, and reverses directions. | T2 | 4 |
| MB-CURVE-027 | Trims | Trims surfaces with curves on surface, keeping or discarding regions, and untrims. | T3 | 4 |
| MB-CURVE-028 | Fillet and blend surfaces | Creates fillet surfaces between two surfaces and blend surfaces between edges with continuity choice (position, tangent, curvature). | T3 | 5 |
| MB-CURVE-029 | Stitch | Stitches surface edges to close gaps within a tolerance. | T3 | 5 |
| MB-CURVE-030 | Tessellate to mesh | Converts NURBS surfaces to meshes by fixed, chord-tolerance or count-based tessellation, quad or triangle output, keeping UVs from surface parameters. Optionally keeps the NURBS source live upstream. | T2 | 4 |

## Strand curves

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CURVE-031 | Strand curve data | Stores large numbers of strand curves attached to a surface with per-strand root coordinates and per-point width; displays them efficiently and exports them as curves. Grooming tools are specified in 27-rigid-cloth-and-hair-dynamics. | T2 | 5 |
| MB-CURVE-032 | Surface attachment | Keeps strand roots attached to a deforming surface through stored surface coordinates. | T2 | 5 |
