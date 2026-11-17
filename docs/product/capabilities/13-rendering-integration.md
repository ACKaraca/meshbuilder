# Rendering integration and viewport rendering

Real-time viewport rendering, viewport capture, render setups and passes, and photoreal rendering
through RenderBuilder over the Arc Scene contract. MeshBuilder never links a path tracer.

## Domain objects

- **Render settings**: resolution, pixel aspect, frame range, camera, output paths and format,
  quality settings forwarded to the renderer.
- **Render setup**: a named configuration of collections, overrides and passes rendered as one job.
- **Render pass (AOV)**: a named output channel requested from the renderer.
- **Render session**: a running connection to a RenderBuilder process for interactive rendering.

## Viewport rendering

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RENDER-001 | Physically based viewport | Shades OpenPBR materials in real time with image-based lighting, scene lights, shadows, screen-space ambient occlusion and reflections, tone mapping through the color pipeline, and transparency sorting. | T1 | 4 |
| MB-RENDER-002 | Viewport effects | Toggles depth of field, motion blur, bloom and anti-aliasing quality per pane. | T2 | 4 |
| MB-RENDER-003 | Studio lighting and matcaps | Lights objects with built-in studio environments or matcap images independent of scene lights, for modeling review. | T1 | 4 |
| MB-RENDER-004 | Stylized display | Draws hidden-line, outline, cavity and flat-color display modes for review and illustration. | T2 | 4 |
| MB-RENDER-005 | Viewport capture | Captures the active camera's viewport for a frame range to an image sequence or video file at a resolution, with optional overlays, burned-in frame and camera labels, and audio. Runs headless from the CLI. Shot-based preview output (MB-ANIM-046) uses this capture path. | T1 | 4 |
| MB-RENDER-006 | Turntable capture | Captures a rotating turntable of the selection or the environment over a frame count without modifying the scene. | T2 | 4 |

## RenderBuilder integration

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RENDER-007 | Arc Scene export | Produces an Arc Scene from the evaluated scene at a frame or frame range: instances, subdivision control meshes, curves, points, volumes, OpenPBR materials, lights, cameras, environment and render settings. | T1 | 4 |
| MB-RENDER-008 | Interactive render view | Starts a RenderBuilder process, streams scene changes as incremental Arc Scene updates, and displays progressive frames in a render view panel or inside a viewport pane, with region render and pause. | T1 | 4 |
| MB-RENDER-009 | Change streaming | Sends only changed objects, transforms, materials and lights after each transaction; a camera move updates within one frame interval of the renderer. | T1 | 4 |
| MB-RENDER-010 | Render to file | Renders the current frame, a frame range or all render setups to image files through RenderBuilder as a background job with progress, cancel and a log. | T1 | 4 |
| MB-RENDER-011 | Render queue | Queues render jobs locally and submits them to a render service on Arc Hub when connected. | T2 | 5 |
| MB-RENDER-012 | Renderer crash isolation | A renderer failure ends the render session with a message and never affects the document or the application. | T1 | 4 |
| MB-RENDER-013 | Frame buffer | Shows rendered images with pass selection, exposure and view transform, comparison against snapshots (wipe and side by side), pixel probing and saving. | T2 | 4 |

## Render setups and passes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-RENDER-014 | Render passes | Requests outputs such as beauty, diffuse, specular, transmission, emission, depth, normal, position, motion vectors, object and material identifiers, matte identifiers and light group passes, with per-pass format and bit depth. | T1 | 4 |
| MB-RENDER-015 | Render setups | Defines named setups that include or exclude collections, set object visibility, matte and shadow-catcher roles, and override properties (absolute or relative) without editing the scene. | T2 | 4 |
| MB-RENDER-016 | Override inspection | Shows which overrides affect a selected object in the active render setup and the resulting values. | T2 | 4 |
| MB-RENDER-017 | Output naming | Builds output file names from tokens (scene, setup, pass, camera, frame, version) and validates them before rendering. | T1 | 4 |
| MB-RENDER-018 | Render settings presets | Saves render settings as presets (draft, review, final) and applies them per setup. | T2 | 4 |
| MB-RENDER-019 | Post effects request | Requests renderer-side imaging effects (denoising, glare, bloom, vignette, chromatic aberration) as part of the render settings. | T2 | 4 |
| MB-RENDER-020 | Render statistics | Reports per-job time, memory, and object and texture counts returned by the renderer. | T2 | 4 |
| MB-RENDER-021 | USD for other renderers | Exports a render-ready USD stage with render settings, products and passes so other USD-capable renderers can render the scene. | T2 | 4 |
