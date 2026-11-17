# Viewport, navigation, selection and manipulators

How users see and touch the scene: viewport layouts and display, camera navigation, object and
component selection, soft selection, snapping, transform manipulators and alignment tools.

## Domain objects

- **Viewport**: a view onto the scene with its own camera, display mode, overlays and filters.
- **Viewport layout**: an arrangement of viewports saved with the workspace.
- **Selection**: the current set of objects or components, with an active element.
- **Manipulator**: an interactive handle set bound to a tool and a reference frame.
- **Snap settings**: enabled snap targets, priorities and increments.

## Viewports and display

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-001 | Viewport layouts | Splits the viewport area into one to four panes in preset or custom arrangements; each pane has its own camera (perspective, orthographic axis views, scene cameras) and display settings. Maximizes and restores a pane with one command. | T1 | 4 |
| MB-VIEW-002 | Display modes | Renders each pane as wireframe, flat, smooth shaded, shaded with wireframe overlay, textured, material preview, or bounding box. Display mode can be overridden per object and per layer. | T1 | 4 |
| MB-VIEW-003 | See-through and x-ray | Draws selected objects, all objects, or joints and components through occluding geometry with adjustable opacity, without changing materials. | T1 | 4 |
| MB-VIEW-004 | Component display | Shows vertices, edges, faces, face centers, normals, borders, non-manifold edges, creases, seams and UV boundaries as overlays with size and color settings. | T1 | 4 |
| MB-VIEW-005 | Heads-up overlays | Shows optional overlays: frame number, camera name, polygon and triangle counts for scene and selection, frame rate, grid units, axis triad, and safe areas. Overlays are not part of rendered output unless requested for viewport capture. | T1 | 4 |
| MB-VIEW-006 | Grid and backgrounds | Draws an adaptive ground grid in document units with major and minor lines, and per-pane background color, gradient, image or environment. | T1 | 4 |
| MB-VIEW-007 | Image planes | Places reference images or image sequences on planes attached to a camera or free in the scene, with opacity, depth placement and frame offset. | T1 | 4 |
| MB-VIEW-008 | Adaptive display quality | Lowers display detail (subdivision preview level, texture resolution, effects, distant objects as boxes) during navigation when frame time exceeds a target, and restores full quality when idle. | T2 | 4 |
| MB-VIEW-009 | Large-scene display | Draws instanced content with GPU instancing, culls by frustum and occlusion, and streams geometry of unloaded or proxy objects so scenes with hundreds of millions of instanced triangles remain navigable. | T2 | 4 |
| MB-VIEW-010 | Vendor-neutral GPU | The viewport runs on any GPU supported by the platform graphics APIs (Vulkan, Metal, Direct3D 12) through one code path, with identical features on Windows, macOS and Linux. | T1 | 4 |
| MB-VIEW-011 | Camera-relative precision | Renders geometry relative to the viewing camera so objects far from the origin display without jitter at f64 model precision. | T1 | 4 |

## Navigation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-012 | Orbit, pan, zoom | Navigates with mouse, trackpad, tablet or keyboard; orbit pivots around the selection, the point under the cursor or the view center according to a setting. Navigation bindings are configurable presets. | T1 | 4 |
| MB-VIEW-013 | Frame commands | Frames the selection, all objects, or the component selection in the active or all panes, animating the transition when enabled. | T1 | 4 |
| MB-VIEW-014 | Orientation widget | Shows a clickable orientation cube with faces, edges and corners for standard views and drag-to-orbit; switches between perspective and orthographic. | T1 | 4 |
| MB-VIEW-015 | Walk and fly | Navigates first-person with keyboard and mouse at adjustable speed, with optional gravity and collision against scene geometry. | T2 | 4 |
| MB-VIEW-016 | Look through objects | Looks through any camera, light or object as a view camera; moving the view moves the object when locking is enabled, as an undoable command. | T1 | 4 |
| MB-VIEW-017 | View bookmarks | Saves named view positions per pane and recalls them; bookmarks are stored in the document. | T2 | 4 |
| MB-VIEW-018 | 3D input devices | Accepts six-degree-of-freedom navigation devices through platform input interfaces, with sensitivity and axis mapping settings. | T3 | 5 |

## Selection

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-019 | Pick and region selection | Selects by click, rectangle, circle, lasso and paint brush, with replace, add, subtract, toggle and intersect modifiers, and window (fully inside) or crossing (touching) region modes. | T1 | 4 |
| MB-VIEW-020 | Selection filters | Restricts picking by object type (mesh, curve, light, camera, empty, joint, points, volume) and by layer selectability; filters are combinable and saved per workspace. | T1 | 4 |
| MB-VIEW-021 | Component modes | Switches between object, vertex, edge, face, element (connected shell) and UV modes with one key each; a mixed mode allows vertices, edges and faces in one selection. | T1 | 4 |
| MB-VIEW-022 | Convert selection | Converts a component selection to another type: to vertices, edges, faces, borders, shells, contained or touching variants. | T1 | 4 |
| MB-VIEW-023 | Grow, shrink and connected | Grows or shrinks the component selection by one ring of adjacency, selects connected elements, and selects by normal angle continuity up to a threshold. | T1 | 4 |
| MB-VIEW-024 | Loops and rings | Selects edge loops, edge rings, face loops and border loops from one or two picked elements, including partial loops between two picks and every-Nth selection. | T1 | 4 |
| MB-VIEW-025 | Select by criteria | Selects components by face side count, area, edge length, angle, normal direction, material, attribute value range, open borders or non-manifold condition. | T2 | 4 |
| MB-VIEW-026 | Backface handling | Region selection either ignores or includes occluded and back-facing components according to a toggle, independent of display. | T1 | 4 |
| MB-VIEW-027 | Selection lock and history | Locks the selection against accidental change, and steps backward and forward through recent selections without affecting document undo. | T2 | 4 |

## Soft selection

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-028 | Soft selection | Extends component transforms to nearby components with a falloff radius and curve; falloff measures by volume distance, surface (geodesic) distance or topological steps. Falloff is displayed as a color ramp on the mesh. | T1 | 4 |
| MB-VIEW-029 | Interactive falloff | Adjusts the falloff radius by a held key and drag in the viewport, with live preview. | T1 | 4 |
| MB-VIEW-030 | Painted soft selection | Paints per-component selection weights with a brush (add, remove, smooth), producing a weighted selection usable by any transform or weighted modifier. | T2 | 4 |

## Symmetry

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-031 | Symmetric editing | Mirrors selection and transforms across a world or object plane, or across topology (mirror map computed from a picked center edge loop), so posed or asymmetric meshes can still be edited symmetrically. | T1 | 4 |
| MB-VIEW-032 | Symmetry tolerance | Reports components without a symmetric partner within the tolerance and lets the user snap them onto the mirror plane or to their counterpart. | T2 | 4 |

## Manipulators and transforms

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-033 | Move, rotate, scale | Provides translation, rotation and scale manipulators with axis, plane and screen-space handles, plus a combined manipulator. Dragging produces one undoable command per drag. | T1 | 4 |
| MB-VIEW-034 | Reference frames | Orients manipulators in world, parent, local, view, gimbal, normal (average of selected components), active element, or a custom picked frame. | T1 | 4 |
| MB-VIEW-035 | Transform pivot point | Uses the object pivot, selection center, bounding box center, individual origins, 3D cursor or a temporary working pivot as the transform center. | T1 | 4 |
| MB-VIEW-036 | Numeric transform entry | Accepts absolute and relative values, unit expressions and arithmetic in a type-in panel or on the command line during a drag. | T1 | 4 |
| MB-VIEW-037 | Constrained component moves | Slides components along edges, along face planes or along normals, preserving UVs when requested. | T1 | 4 |
| MB-VIEW-038 | Custom manipulators | Modifiers, lights, cameras and node-graph parameters expose their own manipulators (gizmos) for parameters such as falloff, angle or direction; plug-ins can define manipulators. | T2 | 4 |

## Snapping and alignment

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-VIEW-039 | Snapping | Snaps to grid, vertices, edges, edge midpoints, face centers, faces, curves, pivots and the live surface of a designated object, with priority order and a visible snap marker. | T1 | 4 |
| MB-VIEW-040 | Increment snapping | Snaps translation, rotation and scale to increments set in document units, degrees and percent. | T1 | 4 |
| MB-VIEW-041 | Construction planes | Draws and creates on a temporary plane picked from a face, three points, or a view; new primitives orient to the plane. | T2 | 4 |
| MB-VIEW-042 | Align | Aligns objects' positions, orientations or scales to a target's pivot, bounds minimum, center or maximum per axis, and aligns an object's axis to a picked surface normal. | T1 | 4 |
| MB-VIEW-043 | Distribute | Distributes selected objects evenly along an axis, a curve or between two objects, by spacing or by count. | T2 | 4 |
| MB-VIEW-044 | Place on surface | Drops or paints objects onto a target surface aligned to its normal, with random rotation and scale ranges; each placed object is a copy or instance. | T2 | 4 |
