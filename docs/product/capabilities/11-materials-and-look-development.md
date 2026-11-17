# Materials and look development

Materials authored on the Arc Scene material model: OpenPBR surfaces, MaterialX node graphs, texture
handling, assignment, libraries and the look-development environment.

## Domain objects

- **Material**: a named shading network whose terminal is an OpenPBR surface (plus optional
  displacement and volume terminals), stored as a MaterialX-compatible node graph.
- **Texture asset**: an image or image sequence stored in the package or referenced by path, with
  color space, UDIM pattern and filtering settings.
- **Material assignment**: binding of a material to an object, a collection or a face set.
- **Material library**: a document or folder of materials available for reuse.

## Material model

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LOOK-001 | OpenPBR surface | Provides the OpenPBR surface parameter set (base, specular, metalness, transmission, subsurface, coat, fuzz, thin film, emission, geometry) as the standard surface model; viewport and RenderBuilder interpret the same parameters. | T1 | 4 |
| MB-LOOK-002 | Material graphs | Edits materials as node graphs in the shared node editor using the MaterialX standard node library (math, textures, procedurals, color, geometry inputs) plus Arc-specific nodes that declare a MaterialX expansion. | T1 | 4 |
| MB-LOOK-003 | Displacement and bump | Accepts scalar and vector displacement, bump and normal maps with space, scale and midlevel; displacement subdivision is requested from the renderer through the Arc Scene. | T1 | 4 |
| MB-LOOK-004 | Volume and hair shading | Provides volume (density, scattering, absorption, emission) and strand (hair and fiber) material terminals. | T2 | 5 |
| MB-LOOK-005 | Layering and mixing | Mixes and layers surfaces by masks, with a layer stack view that edits the underlying graph. | T2 | 4 |
| MB-LOOK-006 | Procedural textures | Provides noise, cellular, gradient, ramp, checker, brick, wood-grain and fractal texture nodes in 2D and 3D, plus triplanar projection. | T1 | 4 |
| MB-LOOK-007 | Geometry and attribute inputs | Reads UV maps, colors, any exported attribute, position, normal, curvature, ambient occlusion approximations and object and instance identifiers as node inputs. | T1 | 4 |
| MB-LOOK-008 | Toon and stylized shading | Provides non-photoreal surface terminals (cel bands, outline width attribute, flat color) interpreted by RenderBuilder, with a viewport approximation. | T3 | 5 |
| MB-LOOK-009 | Shading language nodes | Accepts custom shader nodes written in Open Shading Language as opaque nodes passed to the renderer, with a declared fallback for the viewport. | T3 | 5 |

## Textures

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LOOK-010 | Texture loading | Loads PNG, JPEG, TIFF, OpenEXR (including multi-part), HDR and KTX2 images, image sequences and UDIM sets, with background loading and mip-mapped display. | T1 | 4 |
| MB-LOOK-011 | Texture color spaces | Assigns an input color space to every texture from the active color configuration, with rules by file name and type (color data versus raw data). | T1 | 4 |
| MB-LOOK-012 | Texture embedding and paths | Embeds textures into the package as content-addressed assets or references them by project-relative path; switches between the two per texture or in bulk. | T1 | 4 |
| MB-LOOK-013 | Texture placement | Transforms texture coordinates (offset, rotation, scale, tiling, mirroring) per texture node, and selects the UV map. | T1 | 4 |
| MB-LOOK-014 | Texture reload and watch | Reloads textures when files change on disk, with a manual reload command. | T1 | 4 |
| MB-LOOK-015 | Bake graph to texture | Renders a procedural texture subgraph to an image at a resolution for export to formats without procedural support. | T2 | 4 |

## Assignment

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LOOK-016 | Assign materials | Assigns materials to objects, collections or selected faces by command or drag-and-drop in the viewport; per-face assignment uses material slots. | T1 | 4 |
| MB-LOOK-017 | Material slots | Lists materials used by an object in ordered slots; reorders, replaces and removes slots and selects faces by slot. | T1 | 4 |
| MB-LOOK-018 | Instance material overrides | Overrides material bindings on an instance without breaking the instance, with overrides stored per instance. | T2 | 4 |
| MB-LOOK-019 | Select by material | Selects all objects or faces that use a material, and replaces one material with another scene-wide. | T1 | 4 |
| MB-LOOK-020 | Material variants | Stores alternative material sets for an object or collection as named variants and switches between them; variants export as USD variant sets. | T2 | 4 |

## Look-development environment

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LOOK-021 | Material browser | Shows document materials and libraries as thumbnails with search, tags and drag-and-drop; thumbnails render in the background. | T1 | 4 |
| MB-LOOK-022 | Material preview | Previews a material on a chosen shape (sphere, cloth, shader ball, selected object) under a chosen environment in the viewport renderer, and on request through RenderBuilder. | T1 | 4 |
| MB-LOOK-023 | Look-development stage | Opens a workspace with turntable, environment rotation, reference charts (gray and chrome spheres, color checker) and side-by-side comparison of materials. | T2 | 4 |
| MB-LOOK-024 | Material libraries | Saves materials to libraries with thumbnails and metadata and imports them into documents by copy or by reference. | T1 | 4 |
| MB-LOOK-025 | MaterialX document exchange | Imports and exports materials as MaterialX documents with textures, mapping legacy shading models (Phong, Lambert, glTF PBR, other surface standards) to OpenPBR and reporting approximations. | T1 | 4 |
| MB-LOOK-026 | Material conversion report | Lists materials and nodes that the viewport approximates or the target format cannot express, with the substitution used. | T2 | 4 |
