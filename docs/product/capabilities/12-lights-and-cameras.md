# Lights and cameras

Light sources, environments, light linking, and physically described cameras with exposure, depth of
field and projection types. Shot sequencing and camera switching over time are specified in
[22-keyframe-animation.md](22-keyframe-animation.md).

## Domain objects

- **Light**: point, spot, directional (distant), area (rectangle, disk, sphere, cylinder), mesh
  emitter, environment (dome) or portal, with intensity in photometric or radiometric units.
- **Light filter**: gobo, barn door, blocker or falloff applied to a light.
- **Environment**: image-based or procedural sky lighting and background for the scene.
- **Camera**: perspective, orthographic, fisheye, panoramic or stereo camera with a physical sensor.

## Lights

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LIGHT-001 | Light types | Creates point, spot, directional, rectangle, disk, sphere and cylinder lights with color, color temperature, intensity and exposure, shadow toggle and visibility to camera, diffuse and specular. | T1 | 4 |
| MB-LIGHT-002 | Physical units | Expresses intensity in watts, lumens, candela, lux or nits as appropriate per type, converting exactly between units when the user switches. | T1 | 4 |
| MB-LIGHT-003 | Spot cone and falloff | Sets cone angle, softness and falloff with viewport manipulators. | T1 | 4 |
| MB-LIGHT-004 | Photometric profiles | Loads IES photometric profiles onto point and spot lights and displays their distribution in the viewport. | T2 | 4 |
| MB-LIGHT-005 | Mesh lights | Turns any mesh into an emitter with intensity normalized by area or not. | T2 | 4 |
| MB-LIGHT-006 | Light filters | Attaches gobo textures, barn doors, blockers and distance falloff curves to lights. | T2 | 5 |
| MB-LIGHT-007 | Portals | Places portal lights at openings to guide environment sampling in interiors. | T2 | 4 |
| MB-LIGHT-008 | Light linking | Includes or excludes objects or collections per light for illumination and separately for shadows. | T2 | 4 |
| MB-LIGHT-009 | Light editor | Lists all lights in a table with on/off, solo, intensity, color, exposure and grouping, editable in bulk. | T1 | 4 |
| MB-LIGHT-010 | Light groups | Assigns lights to named groups that the renderer outputs as separate light passes. | T2 | 4 |
| MB-LIGHT-011 | Place by reflection | Positions a light by clicking the point on a surface where its highlight or reflection should appear from the current view. | T3 | 4 |

## Environments

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LIGHT-012 | Image-based lighting | Lights the scene from an equirectangular or cube-map HDR image with rotation, intensity, and separate visibility to camera, reflections and refraction. | T1 | 4 |
| MB-LIGHT-013 | Physical sky | Generates sun and sky lighting from sun angles or from location, date and time (with time zone and daylight saving), and animates the sun over time. | T2 | 4 |
| MB-LIGHT-014 | Backplates and ground | Sets a background image separate from the lighting environment and a shadow-catching ground plane. | T2 | 4 |
| MB-LIGHT-015 | Atmosphere | Adds uniform or height-falloff fog and atmospheric volume to the scene for the renderer, with a viewport approximation. | T2 | 5 |

## Cameras

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-LIGHT-016 | Physical camera | Defines focal length, sensor size and fit, aperture, shutter, ISO and exposure; changing sensor or lens keeps the field of view consistent with the chosen fit rule. | T1 | 4 |
| MB-LIGHT-017 | Clip planes and overscan | Sets near and far clip planes and overscan around the render resolution. | T1 | 4 |
| MB-LIGHT-018 | Depth of field | Sets focus distance (numeric, picked on a surface, or tracked to an object), aperture shape (blades, rotation, anamorphic ratio), displayed in the viewport. | T1 | 4 |
| MB-LIGHT-019 | Motion blur settings | Sets shutter open and close relative to the frame and shutter curve, passed to the renderer. | T2 | 4 |
| MB-LIGHT-020 | Projection types | Supports perspective, orthographic, fisheye (equidistant, equisolid), equirectangular and cylindrical panoramic projections. | T2 | 4 |
| MB-LIGHT-021 | Lens shift and correction | Applies horizontal and vertical lens shift and two-point perspective correction without tilting the camera. | T2 | 4 |
| MB-LIGHT-022 | Stereo cameras | Creates stereo pairs with interaxial distance and convergence modes, and previews anaglyph or side-by-side in the viewport. | T3 | 5 |
| MB-LIGHT-023 | Camera aim and up | Creates cameras with an aim target and optional up target, and creates a camera from the current view. | T1 | 4 |
| MB-LIGHT-024 | Gates and guides | Displays film gate, resolution gate, safe action and safe title areas, and composition guides (thirds, golden ratio, center) in camera views. | T1 | 4 |
| MB-LIGHT-025 | Camera matching | Matches a camera to a background image by picking corresponding points or vanishing lines and solving position, rotation and focal length. | T3 | 5 |
| MB-LIGHT-026 | Camera projection | Projects an image from a camera onto scene geometry as a texture, for matte painting and set extension. | T3 | 5 |
