# Color management and project settings

Scene-linear color handling through OpenColorIO, document units, time and frame rates, project
folders and file rules.

## Domain objects

- **Color configuration**: the OpenColorIO configuration in use (built-in or external), with the
  working space, display, view and look selections.
- **Document settings**: units, up axis record, frame rate, time range, start frame and timecode.
- **Project**: a folder with a project file defining folder roles, path variables and defaults.

## Color management

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PROJ-001 | OpenColorIO configuration | Uses a built-in ACES-based OpenColorIO configuration by default and accepts an external configuration per user, per project or by environment variable. | T1 | 4 |
| MB-PROJ-002 | Working color space | Stores the scene-linear working space as a document setting; all color values and textures convert into it, and the Arc Scene declares it to the renderer. | T1 | 4 |
| MB-PROJ-003 | Display and view transforms | Applies the chosen display, view and optional look to viewports, the UV editor, the frame buffer and thumbnails, per window. | T1 | 4 |
| MB-PROJ-004 | Color pickers | Color pickers edit in scene-linear values and show display-transformed swatches; they accept values in any configured space and sample colors from the screen with the transform inverted. | T1 | 4 |
| MB-PROJ-005 | Input space rules | Assigns texture input spaces by file rules (name patterns, extensions, bit depth) defined in the project, overridable per texture. | T1 | 4 |
| MB-PROJ-006 | Output color spaces | Writes viewport captures and render outputs in a chosen output space per format, with a scene-linear option for OpenEXR. | T1 | 4 |
| MB-PROJ-007 | Color configuration check | Reports missing or mismatched color spaces when a document or texture references spaces absent from the active configuration, and offers a mapping. | T2 | 4 |

## Units, time and coordinates

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PROJ-008 | Display units | Stores lengths in metres internally and displays them in a document unit (millimetre to kilometre, inch, foot), with unit expressions accepted in every numeric field. | T1 | 4 |
| MB-PROJ-009 | Scene scale presets | Sets typical scene scales (product, character, architecture, environment) that adjust grid, clip planes and default primitive sizes without changing stored values. | T2 | 4 |
| MB-PROJ-010 | Frame rate and time range | Stores a rational frame rate (including fractional NTSC rates), start and end frames, and playback range; changing the frame rate keeps keys at their times or at their frame numbers, as chosen. | T1 | 4 |
| MB-PROJ-011 | Timecode | Displays time as frames, seconds or timecode with a start timecode setting. | T2 | 4 |
| MB-PROJ-012 | Coordinate reference | Records a local origin and an optional geographic reference, used when linking georeferenced Arc Scenes so they align without moving far-from-origin geometry. | T2 | 4 |

## Projects

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-PROJ-013 | Project folders | Creates and opens projects with folder roles (scenes, textures, caches, renders, captures, references, assets, scripts) and resolves relative paths against them. | T1 | 4 |
| MB-PROJ-014 | Project defaults | Stores defaults per project: units, frame rate, color configuration, render presets, export presets and naming rules, applied to new documents. | T1 | 4 |
| MB-PROJ-015 | Path variables | Defines path variables (for example a shared asset root) per project or machine, so documents move between machines and operating systems unchanged. | T1 | 4 |
| MB-PROJ-016 | Autosave | Autosaves to the recovery journal at an interval and on idle, without blocking interaction, independent of explicit saves. | T1 | 4 |
| MB-PROJ-017 | Naming rules | Validates object, material and output names against project rules (patterns, prefixes, forbidden characters) and reports or fixes violations in bulk. | T2 | 4 |
