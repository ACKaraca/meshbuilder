# Sequencing, compositing and drawing animation

Video sequencing and image compositing at the level needed to review and finish shots, and
drawing-based 2D animation in the 3D scene. The sequence editor assembles clips, cuts, transitions
and audio; the node compositor combines render passes; drawings are stroke animation placed in the
scene. This file carries two area tokens: `COMP` (sequencing and compositing) and `DRAW2D`
(drawing-based animation). Scene shots and the shot list belong to
[22-keyframe-animation.md](22-keyframe-animation.md); this file edits and finishes their output.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Edit sequence | Frame rate, resolution, tracks, markers, output range | One or more per document; independent of the scene shot list but can mirror it |
| Clip | Source (image sequence, movie, shot, composite, title, solid), in and out points, position, speed, transform, opacity, blend mode | Placed on a video or audio track |
| Transition | Kind, duration, easing, parameters | Joins two clips or fades one |
| Audio track | Clips, gain, pan, fades, mute, solo | Mixed to the sequence output |
| Compositing graph | Nodes, links, inputs, outputs, color space | Stored in the document on `arc-nodes`; input from render passes and clips |
| Composite node | Type, parameters, input and output image channels | Part of a compositing graph |
| Mask | Splines with feather, per-frame keys, blend mode | Input to composite nodes and clips |
| Drawing object | Layers, frames with exposure, placement (view plane, surface, free plane) | Scene object; strokes stored as chunked assets ([ADR-0006](../../adr/0006-dense-geometry-as-chunked-assets.md)) |
| Stroke | Points with pressure and thickness, material, fill | Element of a drawing frame |

## Sequence editor

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-COMP-001 | Sequence timeline | Edits clips on any number of video and audio tracks with snapping, zoom, multi-selection, markers and a frame-accurate playhead; the sequence has its own frame rate and resolution and plays against the frame cache and proxies. | T3 | 5 |
| MB-COMP-002 | Media clips | Places image sequences, movies decoded through platform libraries, titles and solid colors as clips; relinks moved media by folder search and generates proxies in the background. | T3 | 5 |
| MB-COMP-003 | Shot clips | Places a scene shot as a clip that shows the playback cache or the latest render, marks the clip out of date when the shot changes and re-renders it on request (MB-ANIM-046, MB-RENDER-010). | T3 | 5 |
| MB-COMP-004 | Edit operations | Splits, trims, ripples, rolls, slips and slides clips, closes gaps, extends by handles and replaces a clip's source while keeping its position and effects. | T3 | 5 |
| MB-COMP-005 | Transitions | Applies dissolve, dip, wipe and push transitions with duration and easing between clips, and fades at track ends. | T3 | 5 |
| MB-COMP-006 | Retiming | Changes clip speed with constant or keyed ramps, freeze frames and reverse, using frame blending or nearest frame. | T3 | 5 |
| MB-COMP-007 | Clip transform and blend | Positions, scales, rotates and crops clips, sets opacity and blend mode, and applies a clip or a mask to a range of tracks as an adjustment. | T3 | 5 |
| MB-COMP-008 | Titles and overlays | Creates text and shape overlays with font, size, color and keyed animation, and burn-in of sequence, shot and frame data. | T3 | 5 |
| MB-COMP-009 | Color management in the sequence | Assigns an input color space per clip, converts through the active OpenColorIO configuration to the sequence working space, and shows the view transform and scopes (waveform, vectorscope, histogram). | T3 | 5 |
| MB-COMP-010 | Audio tracks | Edits audio clips with waveform display, gain, pan, fades and cross-fades, mutes and solos, and keeps audio in sync through scrubbing and playback. | T3 | 5 |
| MB-COMP-011 | Audio metering | Shows level and loudness meters, mixes tracks to the sequence output and exports stems by track group. | T3 | 5 |
| MB-COMP-012 | Review markers | Places frame-accurate markers and notes on the sequence, exports them with the edit list and submits a rendered cut for review through Hub (MB-ANIM-047). | T3 | 5 |
| MB-COMP-013 | Edit list exchange | Reads and writes OpenTimelineIO and CMX-style edit lists with clip names, source ranges, speed changes and transitions, and reports effects the format cannot carry. | T3 | 5 |
| MB-COMP-014 | Sequence render | Renders the mixed sequence to a movie with codec, bit depth and audio options or to an image sequence, as a background job, headless from the CLI. | T3 | 5 |

## Node compositor

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-COMP-015 | Compositing graph | Edits a node graph of floating-point images on the shared node engine, with typed inputs for color, depth, vectors and masks, tiled evaluation on worker threads, GPU nodes where available and a viewer with channel and region selection. | T3 | 5 |
| MB-COMP-016 | Render pass input | Reads multi-layer OpenEXR output of a render job and exposes each pass (MB-RENDER-014) as an output of an input node; the node refreshes when the job is rendered again. | T3 | 5 |
| MB-COMP-017 | Grading nodes | Provides exposure, gamma, lift-gamma-gain, curves, hue and saturation, white balance, lookup tables and color-space conversion nodes. | T3 | 5 |
| MB-COMP-018 | Merge, mattes and masks | Combines images with blend operators and premultiplication control, builds mattes from color or luminance keys, and draws animated spline masks with feather. | T3 | 5 |
| MB-COMP-019 | Filter and transform nodes | Provides blur, sharpen, glow, defocus, motion blur from vectors, noise and grain, resize, crop, translate, rotate, lens distortion and corner pin nodes. | T3 | 5 |
| MB-COMP-020 | Pass-based adjustment | Changes lighting contributions, relights with normal and position passes, adds fog and depth-based defocus from the depth pass, and remaps vectors without re-rendering the scene. | T3 | 5 |
| MB-COMP-021 | Identifier mattes | Extracts mattes of objects, materials and light groups from identifier passes by name or by picking in the viewer. | T3 | 5 |
| MB-COMP-022 | Composite caching | Caches node results per frame under a memory and disk budget, invalidates by revision when an input or a parameter changes and fills the cache in the background over a frame range. | T3 | 5 |
| MB-COMP-023 | Composite output | Writes composites to OpenEXR, image or movie files and offers them as clips in the edit sequence (MB-COMP-002), where a changed composite marks the clip out of date. | T3 | 5 |
| MB-COMP-024 | Post-render composite | Attaches a compositing graph to a render setup so that a finished render job runs the graph over the produced passes and writes the result; runs headless from the CLI and as a Hub job. | T3 | 5 |

## Drawing-based animation in the 3D scene

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-DRAW2D-001 | Drawing object | Creates a drawing object whose layers hold stroke frames, placed on the view plane, on a free plane, on a surface of the scene or at a depth per stroke, with transforms like any scene object. | T3 | 5 |
| MB-DRAW2D-002 | Drawing tools | Draws with pen, brush, eraser and line tools with pressure, tilt and stabilization, erases by stroke or by pixel, and edits points and thickness of existing strokes. | T3 | 5 |
| MB-DRAW2D-003 | Drawing frames and exposure | Keys drawings at frames, holds each for a number of frames, lists them on an exposure sheet and duplicates, inserts and reorders frames without moving other keys. | T3 | 5 |
| MB-DRAW2D-004 | Layers and onion skin | Organizes strokes into layers with opacity, blend mode and visibility, and shows previous and next frames with tint and range for timing. | T3 | 5 |
| MB-DRAW2D-005 | Fills | Fills closed and nearly closed regions with a gap tolerance, with solid color, gradient or material, keeping the fill linked to its boundary strokes. | T3 | 5 |
| MB-DRAW2D-006 | Drawing in 3D space | Draws on a chosen plane or on the surface under the pen, reprojects strokes to another plane and converts strokes to curves or meshes of the scene and back. | T3 | 5 |
| MB-DRAW2D-007 | Stroke modifiers | Applies smoothing, noise, offset, thickness, build-on and time-offset modifiers to strokes on the shared node engine, in the stack or in a graph (MB-GRAPH-001). | T3 | 5 |
| MB-DRAW2D-008 | Line art from geometry | Generates contour, silhouette, crease, intersection and material-boundary strokes from selected objects through a camera, updating with animation and keeping the result editable by layer. | T3 | 5 |
| MB-DRAW2D-009 | Drawing interpolation | Creates in-between drawings from two keyed drawings by stroke correspondence with editable matching, easing and breakdown keys; no generated result is applied without preview. | T3 | 5 |
| MB-DRAW2D-010 | Drawing rigging | Parents drawing layers to joints, deforms them with lattices and joints and drives drawing selection from controls and animation curves. | T3 | 5 |
| MB-DRAW2D-011 | Drawing rendering | Sends strokes and fills to RenderBuilder as ribbons and surfaces with materials and thickness, or renders them in the viewport capture path (MB-RENDER-005) for review. | T3 | 5 |
| MB-DRAW2D-012 | Drawing exchange | Imports and exports vector drawings as SVG per frame, strokes as curves in OpenUSD and Alembic, and drawing animation as image sequences with alpha. | T3 | 5 |
