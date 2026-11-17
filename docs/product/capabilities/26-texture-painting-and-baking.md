# Texture painting and baking

This area specifies painting of physically based material channels directly on models and the baking of maps from high-resolution geometry or material networks. Painting uses layered, non-destructive channel stacks aligned with the Arc Scene material model (OpenPBR and MaterialX vocabulary), and baking produces the maps that games, real-time viewers and RenderBuilder consume.

## Domain objects

| Object | Key properties | Relationships |
|---|---|---|
| Texture Set | Name, resolution, bit depth, channel list, UV or per-face mode, tile layout | Assigned to mesh faces or material slots; owns Channel stacks |
| Channel | Material parameter (base color, roughness, metalness, normal, height, emission, opacity, subsurface, coat and others), color space, bit depth | Member of a Texture Set; maps to an input of a material |
| Paint Layer | Name, blend mode, opacity, mask, visibility, per-channel contents, group | Stacked in a Texture Set; paint or fill content |
| Layer Mask | Raster or procedural mask, invert, blur | Attached to a Paint Layer or group |
| Fill Generator | Procedural source (noise, pattern, gradient, curvature, ambient-occlusion based, edge wear, from bake), parameters | A layer content type; regenerates when parameters or geometry change |
| Projection Source | Image, camera transform, tiling, stencil flag | Used by projection and stencil painting |
| UV Tile | Tile index in a tile numbering scheme, resolution override | Subdivides a Texture Set's UV space |
| Face Texture Grid | Per-face resolution, adjacency data | Texture storage on faces without UVs |
| Bake Job | Source meshes or materials, target mesh, map types, resolution, cage, ray distance, padding, samples, output | Produces image maps stored in the document or on disk |
| Cage | Mesh offset from the target, per-vertex offset, visibility | Bounds ray start for a Bake Job |
| Export Preset | Channel packing, format, bit depth, naming, color space, normal convention | Applied to Texture Sets and Bake Job outputs |

## Texture sets and channels

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-001 | Texture sets | A texture set groups the channels of one material for a set of faces, with its own resolution; sets are created from material slots and edited without reassigning faces. | T1 | 5 |
| MB-PAINT-002 | Physically based channels | Standard channels cover base color, metalness, roughness, specular level, normal, height, ambient occlusion, emission, opacity, subsurface, coat, sheen and anisotropy, mapped to the corresponding inputs of the Arc Scene material. | T1 | 5 |
| MB-PAINT-003 | Custom channels | A channel is added for any material input or a user value, with a data type (scalar, color, vector), bit depth and color space. | T2 | 5 |
| MB-PAINT-004 | Resolution and bit depth | Each texture set and channel has a resolution up to the hardware limit and a bit depth of 8, 16 or 32 bits; changing resolution resamples the paint with a stated filter. | T1 | 5 |
| MB-PAINT-005 | Color management | Channels carry a color space; color channels use a scene-referred or display-referred space through OpenColorIO, and data channels are linear; the viewport shows the display transform. | T1 | 5 |
| MB-PAINT-006 | Live material preview | The viewport renders the painted channels through the material's real-time shading, with environment lighting and an option to preview through RenderBuilder. | T1 | 5 |

## Paint layers and blend modes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-007 | Paint layers | Layers hold content for any subset of channels, with opacity, visibility and a blend mode; reordering, grouping, duplicating, merging and deleting are commands. | T1 | 5 |
| MB-PAINT-008 | Blend modes | Normal, multiply, screen, overlay, add, subtract, difference, darken, lighten, soft light, hard light, color, hue and others work per channel; normal and height channels use blends that combine correctly. | T1 | 5 |
| MB-PAINT-009 | Layer masks | Any layer or group has a mask that is painted, filled, generated from procedural content, inverted or blurred; masks can be linked across channels. | T1 | 5 |
| MB-PAINT-010 | Layer groups and folders | Layers nest in folders with their own opacity, mask and blend mode, collapsed and exported as a unit. | T1 | 5 |
| MB-PAINT-011 | Layer effects | Adjustment layers apply levels, curves, hue and saturation, color balance, blur, sharpen and gradient maps to the layers below. | T2 | 5 |
| MB-PAINT-012 | Non-destructive history | Strokes and fills are recorded as commands on a layer with undo and a memory budget; the layer's final pixels are re-derived from the stored operations or kept in a baked state by user choice. | T2 | 5 |

## Painting and brushes

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-013 | Paint brushes | Paint, erase, smudge, clone, blur, dodge, burn, fill and eyedropper tools apply to the active layer and channels at once, with size, hardness, flow and opacity. | T1 | 5 |
| MB-PAINT-014 | Multi-channel painting | A brush writes values to several channels in one stroke (for example color, roughness and height), using a material preset or per-channel values. | T1 | 5 |
| MB-PAINT-015 | Brush dynamics | Stamp textures, alpha images, spacing, jitter, scatter, rotation and tablet pressure, tilt and speed responses shape stroke output. | T1 | 5 |
| MB-PAINT-016 | Symmetry in paint | Strokes mirror across axes or topology, and radial symmetry repeats around an axis. | T1 | 5 |
| MB-PAINT-017 | Masked painting | Paint respects selections by faces, by UV island, by color, by mask or by material, so strokes do not cross boundaries. | T1 | 5 |
| MB-PAINT-018 | Stroke correction | Lazy stroke smoothing, straight lines by modifier and path strokes along curves give controlled results. | T2 | 5 |
| MB-PAINT-019 | Sculpt-from-paint height | A height channel painted on a mesh can be applied to the geometry as displacement through the sculpt tools, and the reverse. | T3 | 5 |

## Projection and stencils

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-020 | Projection painting | An image is projected through the viewport camera onto the surface and applied by stroke or by a single apply, with hidden-surface handling and a seam-blend option. | T1 | 5 |
| MB-PAINT-021 | Stencil painting | A stencil image is positioned over the viewport and a stroke applies its pattern to the surface, with move, scale, rotate, flip and tiling controls. | T1 | 5 |
| MB-PAINT-022 | Projection from reference | A set of reference photographs, each with a calibrated camera, can be stored and re-projected onto a model from several angles. | T2 | 5 |
| MB-PAINT-023 | Triplanar projection | A fill layer projects a texture along three axes with blending to avoid UV stretching, in world or object space. | T1 | 5 |
| MB-PAINT-024 | Bake projection to UV | Projection results bake to the texture set's UV layout with padding and a seam fix pass. | T1 | 5 |

## UV tiles and per-face textures

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-025 | Painting across UV tiles | Strokes and fills cross tile borders seamlessly in a tiled UV layout, with per-tile resolution overrides and a tile numbering scheme compatible with common tile conventions. | T1 | 5 |
| MB-PAINT-026 | Tile management | Tiles can be added, removed, renumbered and resized, and shells are moved between tiles with the paint following them. | T2 | 5 |
| MB-PAINT-027 | Per-face texture format | A texture set may store a small texture per face instead of a UV layout, for models without UVs; strokes cross face boundaries using adjacency, and export converts to a per-face texture file or to UVs. | T2 | 5 |
| MB-PAINT-028 | UV-free to UV conversion | A per-face texture set converts to a UV layout with automatic seams and packing, and the paint is resampled with a seam-fix pass. | T3 | 5 |
| MB-PAINT-029 | Seam handling | Padding, dilation and seam blending are applied on every bake and export, with a seam visualization overlay in the viewport. | T1 | 5 |

## Procedural and smart fills

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-030 | Fill layers | A fill layer fills channels with a constant, a texture, or a procedural graph; the output regenerates when parameters or geometry change. | T1 | 5 |
| MB-PAINT-031 | Procedural texture graph | Procedural patterns are built in the node graph editor with noise, patterns, gradients, filters, warps and blends, evaluated to any resolution and exported as MaterialX nodes where expressible. | T1 | 5 |
| MB-PAINT-032 | Geometry-driven masks | Masks are generated from baked or computed curvature, ambient occlusion, thickness, position, world normal and edge distance for wear, dirt and edge highlights. | T1 | 5 |
| MB-PAINT-033 | Smart materials | A saved stack of layers, masks and generators is applied to a mesh as one asset and adapts to the mesh through the geometry-driven masks; materials are saved in libraries. | T2 | 5 |
| MB-PAINT-034 | Material presets | Presets for common surfaces (metal, wood, fabric, plastic, stone and others) set physically plausible values across channels and are editable. | T2 | 5 |
| MB-PAINT-035 | Material import and export | Texture sets and layer stacks round-trip to MaterialX documents and OpenUSD material networks where the graph is expressible, and report anything that is baked on export. | T1 | 5 |
| MB-PAINT-036 | Assisted material generation | An optional assistant proposes a layer stack or a procedural graph from a reference image, shown as editable layers before apply. | T3 | 5 |

## Bake types

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-037 | Normal map bake | Bakes tangent-space or object-space normals from a high-resolution source onto a low-resolution target with a selectable tangent basis that matches the viewer or engine, and a conformance check against a reference. | T1 | 4 |
| MB-PAINT-038 | Height and displacement bake | Bakes a scalar height or displacement map from source to target with a range normalization or absolute units, in 16 or 32 bits. | T1 | 4 |
| MB-PAINT-039 | Vector displacement bake | Bakes a three-component displacement relative to the target surface in tangent or object space as 32-bit image files, usable as vector displacement stamps. | T2 | 4 |
| MB-PAINT-040 | Ambient occlusion bake | Bakes ambient occlusion with ray count, distance, bias and an option to include only the selected objects or the full scene. | T1 | 4 |
| MB-PAINT-041 | Curvature and thickness bake | Bakes convex and concave curvature and local thickness maps with radius and normalization controls. | T1 | 4 |
| MB-PAINT-042 | Position, normal and ID bakes | Bakes object or world position, world normal, object ID, material ID, face ID and UV maps for masking and downstream tools. | T1 | 4 |
| MB-PAINT-043 | Material transfer bake | Bakes the appearance channels of a source's materials (base color, roughness, metalness, emission, opacity) onto the target's texture set, so a complex source collapses to one low-polygon asset. | T1 | 4 |
| MB-PAINT-044 | Lighting bake | Bakes lighting or shadow into a map or into vertex colors through a render of the selected lights, in a form usable by real-time viewers. | T2 | 4 |
| MB-PAINT-045 | Bake from procedurals | Procedural material outputs bake to image maps at a chosen resolution to remove the procedural dependency for export. | T1 | 4 |

## Cage, ray distance and workflow

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-046 | Cage control | A cage is a visible, editable offset of the target mesh; its per-vertex offsets define the start of each ray, and it can be auto-generated by a distance value or authored by hand. | T1 | 4 |
| MB-PAINT-047 | Ray distance and direction | Front and back distances, ray direction (along cage, along normals), self-hit handling and a miss color are parameters, and a debug view shows rays that miss or hit the wrong surface. | T1 | 4 |
| MB-PAINT-048 | Multiple sources and match by name | A target may bake from several source meshes, matched by group name or by an explicit assignment, so exploded bakes avoid cross-contamination. | T1 | 4 |
| MB-PAINT-049 | Bake preview and region | A bake previews in the viewport at low resolution and can be limited to a region of UV space or a subset of faces; progress, cancel and background execution are standard. | T1 | 4 |
| MB-PAINT-050 | Anti-aliasing and padding | Samples per pixel, filter type and dilation padding are parameters, and padding respects shell borders to avoid bleeding. | T1 | 4 |
| MB-PAINT-051 | Bake output | Maps are stored in the document as texture channels or written to files, with a record of bake settings so a bake is repeatable and reproducible by a command. | T1 | 4 |
| MB-PAINT-052 | Headless baking | A bake runs from the command line, from a Hub job and from a script, using the same settings as the interactive bake. | T1 | 4 |

## Export presets

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PAINT-053 | Export presets | A preset maps channels to files, with packing of scalar channels into color channels, file format (PNG, TIFF, OpenEXR and others), bit depth, color space and naming, and presets are shared in project libraries. | T1 | 5 |
| MB-PAINT-054 | Normal map convention | The preset chooses the green-channel direction convention and the tangent basis for exported normal maps, and the choice is stored in the asset metadata. | T1 | 5 |
| MB-PAINT-055 | Engine and format presets | Built-in presets target glTF, OpenUSD and common real-time pipelines, assembling the channel packing each expects. | T1 | 5 |
| MB-PAINT-056 | Export validation | An export checks resolution, bit depth and missing channels against the preset and lists problems before writing files. | T2 | 5 |
